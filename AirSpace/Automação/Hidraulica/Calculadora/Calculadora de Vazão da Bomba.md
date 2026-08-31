

```dataviewjs
// ============================================================
// CALCULADORA DE VAZÃO DA BOMBA
//
// Vazão teórica:
// Qteórica = Vg × n / 1000
//
// Vazão real:
// Qreal = Qteórica × ηv
//
// Vg = cilindrada em cm³/rev
// n  = rotação em rpm
// ηv = eficiência volumétrica
// Q  = vazão em L/min
// ============================================================

const container = dv.el("div", "");
container.className = "calculadora-orificio";

// ------------------------------------------------------------
// Funções para construir a interface
// ------------------------------------------------------------

function criarTitulo(texto, nivel = 2) {
    const titulo = document.createElement(`h${nivel}`);
    titulo.textContent = texto;
    container.appendChild(titulo);
}

function criarCampoNumero(
    rotulo,
    valor,
    minimo,
    maximo,
    passo,
    unidade
) {
    const grupo = document.createElement("div");
    grupo.className = "campo-calculadora";

    const label = document.createElement("label");
    label.textContent = rotulo;

    const linha = document.createElement("div");
    linha.className = "linha-entrada";

    const input = document.createElement("input");
    input.type = "number";
    input.value = valor;
    input.step = passo;

    if (minimo !== null) {
        input.min = minimo;
    }

    if (maximo !== null) {
        input.max = maximo;
    }

    const spanUnidade = document.createElement("span");
    spanUnidade.textContent = unidade;

    linha.appendChild(input);
    linha.appendChild(spanUnidade);

    grupo.appendChild(label);
    grupo.appendChild(linha);
    container.appendChild(grupo);

    return input;
}

function formatarNumero(valor, casas = 2) {
    return valor.toLocaleString("pt-BR", {
        minimumFractionDigits: casas,
        maximumFractionDigits: casas
    });
}

// ------------------------------------------------------------
// Título e entradas
// ------------------------------------------------------------

criarTitulo("Calculadora de Vazão da Bomba");

criarTitulo("Dados da bomba", 3);

const cilindradaInput = criarCampoNumero(
    "Cilindrada da bomba",
    100,
    0,
    null,
    1,
    "cm³/rev"
);

const rotacaoInput = criarCampoNumero(
    "Rotação",
    1500,
    0,
    null,
    100,
    "rpm"
);

const eficienciaInput = criarCampoNumero(
    "Eficiência volumétrica",
    100,
    0,
    100,
    1,
    "%"
);

// ------------------------------------------------------------
// Resultados
// ------------------------------------------------------------

criarTitulo("Resultados", 3);

const resultados = document.createElement("div");
resultados.className = "resultados-calculadora";
container.appendChild(resultados);

// ------------------------------------------------------------
// Cálculo
// ------------------------------------------------------------

function calcular() {
    const Vg = Number(cilindradaInput.value);
    const rpm = Number(rotacaoInput.value);
    const eficiencia = Number(eficienciaInput.value);

    // Validação
    if (
        Vg < 0 ||
        rpm < 0 ||
        eficiencia < 0 ||
        eficiencia > 100
    ) {
        resultados.innerHTML = `
            <div class="aviso-erro">
                Verifique os valores informados. A cilindrada e a
                rotação não podem ser negativas, e a eficiência deve
                estar entre 0% e 100%.
            </div>
        `;
        return;
    }

    // Eficiência em forma decimal
    const etaV = eficiencia / 100;

    // Vazão teórica em L/min
    const QTeorica = (Vg * rpm) / 1000;

    // Vazão real em L/min
    const QReal = QTeorica * etaV;

    // Perda volumétrica interna
    const QPerdida = QTeorica - QReal;

    // Vazão em m³/s
    const QReal_m3s = QReal / 60000;

    resultados.innerHTML = `
        <div class="cartao-resultado">
            <span>Vazão teórica</span>
            <strong>${formatarNumero(QTeorica)} L/min</strong>
        </div>

        <div class="cartao-resultado">
            <span>Vazão real estimada</span>
            <strong>${formatarNumero(QReal)} L/min</strong>
        </div>

        <div class="cartao-resultado">
            <span>Perda volumétrica estimada</span>
            <strong>${formatarNumero(QPerdida)} L/min</strong>
        </div>

        <div class="cartao-resultado">
            <span>Vazão real em unidade SI</span>
            <strong>${QReal_m3s.toExponential(4)} m³/s</strong>
        </div>
    `;
}

// ------------------------------------------------------------
// Atualização automática
// ------------------------------------------------------------

const entradas = [
    cilindradaInput,
    rotacaoInput,
    eficienciaInput
];

entradas.forEach(entrada => {
    entrada.addEventListener("input", calcular);
});

calcular();

// ------------------------------------------------------------
// Fórmulas
// ------------------------------------------------------------

const formulas = document.createElement("div");
formulas.className = "formulas-calculadora";

formulas.innerHTML = `
    <h3>Fórmulas utilizadas</h3>

    <div class="formula">
        Q<sub>teórica</sub> =
        V<sub>g</sub> · n / 1000
    </div>

    <div class="formula">
        η<sub>v</sub> =
        eficiência (%) / 100
    </div>

    <div class="formula">
        Q<sub>real</sub> =
        Q<sub>teórica</sub> · η<sub>v</sub>
    </div>

    <div class="formula">
        Q<sub>perdida</sub> =
        Q<sub>teórica</sub> − Q<sub>real</sub>
    </div>

    <div class="informacao">
        A eficiência volumétrica representa a redução de vazão causada
        principalmente pelas fugas internas da bomba. Ela varia com a
        pressão, a rotação, a temperatura, a viscosidade e o desgaste.
        Por isso, a vazão real calculada é uma estimativa.
    </div>
`;

container.appendChild(formulas);
```
