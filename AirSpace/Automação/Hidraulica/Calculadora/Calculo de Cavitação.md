```dataviewjs
// ==========================================================
// CALCULADORA DE REPOSIÇÃO DE ÓLEO / ANTICAVITAÇÃO
//
// Objetivo:
// Calcular quanto óleo deve entrar em uma câmara de cilindro
// enquanto seu volume aumenta, evitando queda de pressão
// e cavitação.
//
// Vazão geométrica:
// Q [L/min] = A [cm²] × v [mm/s] × 0.006
//
// Balanço volumétrico:
// Qdéficit = Qnecessária - Qentrada
//
// Compressibilidade:
// dp/dt = (Beta / V) × dV/dt
//
// onde:
// Beta = módulo volumétrico efetivo [bar]
// V    = volume hidráulico da câmara [cm³]
// ==========================================================

const container = dv.el("div", "");
container.className = "calculadora-orificio";

// ------------------------------------------------------------
// FUNÇÕES DE INTERFACE
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
    input.min = minimo;
    input.max = maximo;
    input.step = passo;

    const span = document.createElement("span");
    span.textContent = unidade;

    linha.appendChild(input);
    linha.appendChild(span);

    grupo.appendChild(label);
    grupo.appendChild(linha);

    container.appendChild(grupo);

    return input;
}

function criarResultado(rotulo, unidade = "") {
    const linha = document.createElement("div");
    linha.className = "resultado-calculadora";

    const nome = document.createElement("span");
    nome.textContent = rotulo;

    const valor = document.createElement("strong");
    valor.textContent = "0";

    const unit = document.createElement("span");
    unit.textContent = unidade;

    linha.appendChild(nome);
    linha.appendChild(valor);
    linha.appendChild(unit);

    container.appendChild(linha);

    return valor;
}

// ------------------------------------------------------------
// TÍTULO
// ------------------------------------------------------------

criarTitulo("Reposição de óleo / Anticavitação", 2);

// ------------------------------------------------------------
// ENTRADAS
// ------------------------------------------------------------

criarTitulo("Dados do cilindro", 3);

const area = criarCampoNumero(
    "Área efetiva da câmara",
    164.49,
    0.01,
    100000,
    0.01,
    "cm²"
);

const volume = criarCampoNumero(
    "Volume hidráulico atual da câmara",
    40,
    0.01,
    10000000,
    1,
    "cm³"
);

const velocidade = criarCampoNumero(
    "Velocidade de aumento da câmara",
    59,
    0,
    10000,
    0.01,
    "mm/s"
);

criarTitulo("Sistema hidráulico", 3);

const vazaoEntrada = criarCampoNumero(
    "Vazão de óleo realmente entrando",
    0,
    0,
    100000,
    0.1,
    "L/min"
);

const fuga = criarCampoNumero(
    "Fuga / consumo adicional",
    0,
    0,
    10000,
    0.01,
    "L/min"
);

criarTitulo("Pressão e compressibilidade", 3);

const pressaoAtual = criarCampoNumero(
    "Pressão atual da câmara",
    48,
    -1.02,
    1000,
    0.01,
    "bar"
);

const pressaoMinima = criarCampoNumero(
    "Pressão mínima permitida",
    -0.8,
    -1.02,
    1000,
    0.01,
    "bar"
);

const beta = criarCampoNumero(
    "Módulo volumétrico efetivo",
    15000,
    100,
    50000,
    100,
    "bar"
);

// ------------------------------------------------------------
// RESULTADOS
// ------------------------------------------------------------

criarTitulo("Resultados", 3);

const resultadoVazaoMovimento =
    criarResultado(
        "Vazão exigida pelo movimento:",
        " L/min"
    );

const resultadoVazaoTotal =
    criarResultado(
        "Vazão mínima contínua:",
        " L/min"
    );

const resultadoDeficit =
    criarResultado(
        "Déficit de vazão:",
        " L/min"
    );

const resultadoVolumeSegundo =
    criarResultado(
        "Volume criado por segundo:",
        " cm³/s"
    );

const resultadoDpDt =
    criarResultado(
        "Taxa de variação da pressão:",
        " bar/s"
    );

const resultadoTempo =
    criarResultado(
        "Tempo teórico instantâneo até pressão mínima:",
        " s"
    );

// ------------------------------------------------------------
// STATUS
// ------------------------------------------------------------

const status = document.createElement("div");

status.style.marginTop = "16px";
status.style.padding = "12px";
status.style.border =
    "1px solid var(--background-modifier-border)";
status.style.borderRadius = "8px";

container.appendChild(status);

// ------------------------------------------------------------
// CÁLCULO
// ------------------------------------------------------------

function calcular() {
    const A = Number(area.value);
    const V = Number(volume.value);

    const velocidade_mm_s =
        Number(velocidade.value);

    const Qin =
        Number(vazaoEntrada.value);

    const Qfuga =
        Number(fuga.value);

    const P =
        Number(pressaoAtual.value);

    const Pmin =
        Number(pressaoMinima.value);

    const B =
        Number(beta.value);

    // --------------------------------------------------------
    // Vazão causada pelo movimento do pistão
    //
    // A [cm²]
    // v [mm/s]
    //
    // mm/s / 10 = cm/s
    //
    // cm² × cm/s = cm³/s
    //
    // cm³/s × 0.06 = L/min
    //
    // Portanto:
    //
    // Q = A × v(mm/s) × 0.006
    // --------------------------------------------------------

    const Qmov =
        A * velocidade_mm_s * 0.006;

    // --------------------------------------------------------
    // Vazão mínima total
    // --------------------------------------------------------

    const Qnecessaria =
        Qmov + Qfuga;

    // --------------------------------------------------------
    // Déficit
    // --------------------------------------------------------

    const Qdeficit =
        Qnecessaria - Qin;

    // --------------------------------------------------------
    // Volume criado por segundo
    //
    // Q [L/min] → cm³/s
    //
    // 1 L = 1000 cm³
    //
    // Qcm3s = Q × 1000 / 60
    //        = Q / 0.06
    // --------------------------------------------------------

    const volumeCriadoSegundo =
        Qmov / 0.06;

    // --------------------------------------------------------
    // Variação de pressão
    //
    // dP/dt = Beta/V × dV/dt
    //
    // Se faltar óleo:
    //
    // dV/dt = Qdéficit convertido para cm³/s
    // --------------------------------------------------------

    const deficitCm3s =
        Qdeficit / 0.06;

    let dpdt = 0;

    if (V > 0) {
        dpdt =
            -(B / V) * deficitCm3s;
    }

    // --------------------------------------------------------
    // Tempo até atingir pressão mínima
    // --------------------------------------------------------

    let tempo = Infinity;

    if (
        dpdt < 0 &&
        P > Pmin
    ) {
        tempo =
            (P - Pmin) / Math.abs(dpdt);
    }

    // --------------------------------------------------------
    // RESULTADOS
    // --------------------------------------------------------

    resultadoVazaoMovimento.textContent =
        Qmov.toFixed(2);

    resultadoVazaoTotal.textContent =
        Qnecessaria.toFixed(2);

    resultadoDeficit.textContent =
        Qdeficit.toFixed(2);

    resultadoVolumeSegundo.textContent =
        volumeCriadoSegundo.toFixed(2);

    resultadoDpDt.textContent =
        dpdt.toFixed(2);

    if (tempo === Infinity) {
        resultadoTempo.textContent =
            "∞";
    } else {
        resultadoTempo.textContent =
            tempo.toFixed(6);
    }

    // --------------------------------------------------------
    // STATUS
    // --------------------------------------------------------

    if (Qin >= Qnecessaria) {
        status.innerHTML = `
            <strong>✓ Reposição suficiente</strong>
            <br><br>

            A entrada de óleo atende a demanda volumétrica
            causada pelo movimento do cilindro.

            <br><br>

            Margem:

            <strong>
                ${(Qin - Qnecessaria).toFixed(2)} L/min
            </strong>
        `;
    } else {
        status.innerHTML = `
            <strong>⚠ Déficit de óleo</strong>
            <br><br>

            O cilindro está criando volume mais rápido
            do que o óleo está entrando.

            <br><br>

            Faltam:

            <strong>
                ${Qdeficit.toFixed(2)} L/min
            </strong>

            <br><br>

            A pressão tende a cair aproximadamente:

            <strong>
                ${Math.abs(dpdt).toFixed(2)} bar/s
            </strong>
        `;
    }
}

// ------------------------------------------------------------
// EVENTOS
// ------------------------------------------------------------

[
    area,
    volume,
    velocidade,
    vazaoEntrada,
    fuga,
    pressaoAtual,
    pressaoMinima,
    beta

].forEach(input => {
    input.addEventListener(
        "input",
        calcular
    );
});

// ------------------------------------------------------------
// PRIMEIRO CÁLCULO
// ------------------------------------------------------------

calcular();
```