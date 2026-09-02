

```dataviewjs
// ============================================================
// CALCULADORA DE VELOCIDADE DO CILINDRO
//
// v [mm/s] = Q [L/min] × 10000 / (60 × A [cm²])
//
// Relação fundamental:
// Q = A × v
// ============================================================

const container = dv.el("div", "");
container.className = "calculadora-orificio";

// ------------------------------------------------------------
// Funções da interface
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

    const unidadeTexto = document.createElement("span");
    unidadeTexto.textContent = unidade;

    linha.appendChild(input);
    linha.appendChild(unidadeTexto);

    grupo.appendChild(label);
    grupo.appendChild(linha);
    container.appendChild(grupo);

    return input;
}

function formatarNumero(valor, casas = 3) {
    return valor.toLocaleString("pt-BR", {
        minimumFractionDigits: casas,
        maximumFractionDigits: casas
    });
}

// ------------------------------------------------------------
// Título e entradas
// ------------------------------------------------------------

criarTitulo("Calculadora de Velocidade do Cilindro");
criarTitulo("Dados", 3);

const vazaoInput = criarCampoNumero(
    "Vazão",
    220,
    0,
    null,
    1,
    "L/min"
);

const areaInput = criarCampoNumero(
    "Área efetiva do cilindro",
    6310.08,
    0.01,
    null,
    1,
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
    const vazaoLmin = Number(vazaoInput.value);
    const areaCm2 = Number(areaInput.value);

    // Validação
    if (vazaoLmin < 0 || areaCm2 <= 0) {
        resultados.innerHTML = `
            <div class="aviso-erro">
                A vazão não pode ser negativa e a área do cilindro
                deve ser maior que zero.
            </div>
        `;
        return;
    }

    // --------------------------------------------------------
    // Velocidade do cilindro
    // --------------------------------------------------------

    const velocidadeMmS =
        (vazaoLmin * 10000) /
        (60 * areaCm2);

    const velocidadeCmS = velocidadeMmS / 10;
    const velocidadeMS = velocidadeMmS / 1000;
    const velocidadeMmMin = velocidadeMmS * 60;
    const velocidadeMMin = velocidadeMmMin / 1000;

    // Tempo necessário para percorrer 1 metro
    const tempoPorMetroS =
        velocidadeMmS > 0
            ? 1000 / velocidadeMmS
            : 0;

    resultados.innerHTML = `
        <div class="cartao-resultado">
            <span>Velocidade do cilindro</span>
            <strong>
                ${formatarNumero(velocidadeMmS)} mm/s
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Velocidade do cilindro</span>
            <strong>
                ${formatarNumero(velocidadeCmS)} cm/s
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Velocidade em unidade SI</span>
            <strong>
                ${formatarNumero(velocidadeMS, 6)} m/s
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Velocidade por minuto</span>
            <strong>
                ${formatarNumero(velocidadeMMin)} m/min
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Tempo para percorrer 1 metro</span>
            <strong>
                ${
                    velocidadeMmS > 0
                        ? `${formatarNumero(tempoPorMetroS, 2)} s`
                        : "—"
                }
            </strong>
        </div>
    `;
}

// ------------------------------------------------------------
// Atualização automática
// ------------------------------------------------------------

vazaoInput.addEventListener("input", calcular);
areaInput.addEventListener("input", calcular);

calcular();

// ------------------------------------------------------------
// Fórmulas
// ------------------------------------------------------------

const formulas = document.createElement("div");
formulas.className = "formulas-calculadora";

formulas.innerHTML = `
    <h3>Fórmulas utilizadas</h3>

    <div class="formula">
        v [mm/s] =
        Q [L/min] · 10.000 /
        (60 · A [cm²])
    </div>

    <div class="formula">
        v [mm/s] =
        166,6667 · Q [L/min] /
        A [cm²]
    </div>

    <div class="formula">
        v [cm/s] =
        v [mm/s] / 10
    </div>

    <div class="formula">
        t [s] =
        distância [mm] / v [mm/s]
    </div>

    <div class="informacao">
        A velocidade calculada é teórica e considera que toda a vazão
        informada entra no cilindro. A velocidade real pode ser menor
        devido às fugas internas da bomba e do cilindro, à compressão
        do óleo e às perdas de vazão no circuito.
    </div>
`;

container.appendChild(formulas);
```