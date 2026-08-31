

```dataviewjs
// ============================================================
// CALCULADORA DE FORÇA HIDRÁULICA
//
// F = p × A
//
// Usando:
// p em bar
// A em cm²
// F em newtons
//
// Fórmula simplificada:
// F [N] = 10 × p [bar] × A [cm²]
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

criarTitulo("Calculadora de Força Hidráulica");

criarTitulo("Dados", 3);

const pressaoInput = criarCampoNumero(
    "Pressão",
    180,
    0,
    null,
    10,
    "bar"
);

const areaInput = criarCampoNumero(
    "Área efetiva do cilindro",
    5755,
    0,
    null,
    10,
    "cm²"
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
    const pressaoBar = Number(pressaoInput.value);
    const areaCm2 = Number(areaInput.value);

    // Validação
    if (pressaoBar < 0 || areaCm2 < 0) {
        resultados.innerHTML = `
            <div class="aviso-erro">
                A pressão e a área não podem ser negativas.
            </div>
        `;
        return;
    }

    // Força em newtons
    const forcaN = 10 * pressaoBar * areaCm2;

    // Conversões
    const forcaKN = forcaN / 1000;
    const forcaMN = forcaN / 1_000_000;
    const forcaTf = forcaN / 9806.65;

    resultados.innerHTML = `
        <div class="cartao-resultado">
            <span>Força em newtons</span>
            <strong>${formatarNumero(forcaN, 0)} N</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força em quilonewtons</span>
            <strong>${formatarNumero(forcaKN, 2)} kN</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força em meganewtons</span>
            <strong>${formatarNumero(forcaMN, 3)} MN</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força em toneladas-força</span>
            <strong>${formatarNumero(forcaTf, 2)} tf</strong>
        </div>
    `;
}

// ------------------------------------------------------------
// Atualização automática
// ------------------------------------------------------------

pressaoInput.addEventListener("input", calcular);
areaInput.addEventListener("input", calcular);

calcular();

// ------------------------------------------------------------
// Fórmulas
// ------------------------------------------------------------

const formulas = document.createElement("div");
formulas.className = "formulas-calculadora";

formulas.innerHTML = `
    <h3>Fórmula utilizada</h3>

    <div class="formula">
        F [N] =
        10 · p [bar] · A [cm²]
    </div>

    <div class="formula">
        F [tf] =
        F [N] / 9.806,65
    </div>

    <div class="informacao">
        A força calculada é teórica. A força efetivamente disponível
        pode ser menor por causa do atrito das vedações, contrapressão,
        perdas hidráulicas e geometria do mecanismo acionado.
    </div>
`;

container.appendChild(formulas);
```
