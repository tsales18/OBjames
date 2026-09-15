

```dataviewjs
// ==========================================================
// CALCULADORA DE MOLA HIDRÁULICA
//
// DIRETO:
//
// F [N] = K [N/m] × x [m]
//
// p [bar] = F [N] / (10 × A [cm²])
//
// ----------------------------------------------------------
//
// INVERSO POR PRESSÃO:
//
// F [N] = 10 × p [bar] × A [cm²]
//
// K [N/m] = F [N] / x [m]
//
// ----------------------------------------------------------
//
// INVERSO POR FORÇA:
//
// K [N/m] = F [N] / x [m]
//
// ==========================================================

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

    if (minimo !== null) input.min = minimo;
    if (maximo !== null) input.max = maximo;

    const unidadeTexto = document.createElement("span");

    unidadeTexto.textContent = unidade;

    linha.appendChild(input);
    linha.appendChild(unidadeTexto);

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

// ==========================================================
// TÍTULO
// ==========================================================

criarTitulo("Mola — Rigidez, Força e Pressão");

// ==========================================================
// 1. RIGIDEZ → FORÇA → PRESSÃO
// ==========================================================

criarTitulo("Rigidez → Força → Pressão", 3);

const rigidezInput = criarCampoNumero(
    "Rigidez da mola",
    33333,
    0,
    null,
    100,
    "N/m"
);

const compressaoInput = criarCampoNumero(
    "Compressão / pré-carga da mola",
    60,
    0,
    null,
    1,
    "mm"
);

const areaInput = criarCampoNumero(
    "Área hidráulica equivalente",
    1,
    0.0001,
    null,
    0.01,
    "cm²"
);

// ------------------------------------------------------------
// Resultados diretos
// ------------------------------------------------------------

const resultadosDiretos = document.createElement("div");

resultadosDiretos.className = "resultados-calculadora";

container.appendChild(resultadosDiretos);

// ==========================================================
// 2. PRESSÃO / FORÇA → RIGIDEZ
// ==========================================================

criarTitulo("Pressão / Força → Rigidez", 3);

const grupoModo = document.createElement("div");

grupoModo.className = "campo-calculadora";

const labelModo = document.createElement("label");

labelModo.textContent = "Calcular rigidez a partir de";

const selectModo = document.createElement("select");

const opcaoPressao = document.createElement("option");
opcaoPressao.value = "pressao";
opcaoPressao.textContent = "Pressão";

const opcaoForca = document.createElement("option");
opcaoForca.value = "forca";
opcaoForca.textContent = "Força";

selectModo.appendChild(opcaoPressao);
selectModo.appendChild(opcaoForca);

grupoModo.appendChild(labelModo);
grupoModo.appendChild(selectModo);

container.appendChild(grupoModo);

// ------------------------------------------------------------
// Pressão desejada
// ------------------------------------------------------------

const pressaoDesejadaInput = criarCampoNumero(
    "Pressão equivalente desejada",
    200,
    0,
    null,
    1,
    "bar"
);

// ------------------------------------------------------------
// Força desejada
// ------------------------------------------------------------

const forcaDesejadaInput = criarCampoNumero(
    "Força desejada",
    2000,
    0,
    null,
    10,
    "N"
);

// ------------------------------------------------------------
// Área para cálculo inverso
// ------------------------------------------------------------

const areaInversaInput = criarCampoNumero(
    "Área hidráulica",
    1,
    0.0001,
    null,
    0.01,
    "cm²"
);

// ------------------------------------------------------------
// Compressão para cálculo inverso
// ------------------------------------------------------------

const compressaoInversaInput = criarCampoNumero(
    "Compressão / pré-carga",
    60,
    0.001,
    null,
    1,
    "mm"
);

// ------------------------------------------------------------
// Resultados inversos
// ------------------------------------------------------------

const resultadosInversos = document.createElement("div");

resultadosInversos.className = "resultados-calculadora";

container.appendChild(resultadosInversos);

// ==========================================================
// CÁLCULO DIRETO
// ==========================================================

function calcularDireto() {

    const rigidez = rigidezInput.valueAsNumber;
    const compressaoMm = compressaoInput.valueAsNumber;
    const areaCm2 = areaInput.valueAsNumber;

    if (
        !Number.isFinite(rigidez) ||
        !Number.isFinite(compressaoMm) ||
        !Number.isFinite(areaCm2) ||
        rigidez < 0 ||
        compressaoMm < 0 ||
        areaCm2 <= 0
    ) {

        resultadosDiretos.innerHTML = `
            <div class="aviso-erro">
                Preencha os campos com valores válidos.
            </div>
        `;

        return;
    }

    // --------------------------------------------------------
    // mm → m
    // --------------------------------------------------------

    const compressaoM =
        compressaoMm / 1000;

    // --------------------------------------------------------
    // Lei de Hooke
    //
    // F = K × x
    // --------------------------------------------------------

    const forcaN =
        rigidez * compressaoM;

    const forcaKN =
        forcaN / 1000;

    const forcaTf =
        forcaN / 9806.65;

    // --------------------------------------------------------
    // Pressão equivalente
    //
    // p [bar] = F / (10 × A)
    // --------------------------------------------------------

    const pressaoBar =
        forcaN / (10 * areaCm2);

    const pressaoMPa =
        pressaoBar / 10;

    // --------------------------------------------------------
    // Força por mm
    // --------------------------------------------------------

    const forcaPorMm =
        rigidez / 1000;

    // --------------------------------------------------------
    // Pressão por mm
    // --------------------------------------------------------

    const pressaoPorMm =
        forcaPorMm /
        (10 * areaCm2);

    // --------------------------------------------------------
    // Exibição
    // --------------------------------------------------------

    const itens = [

        [
            "Rigidez da mola",
            `${formatarNumero(rigidez, 2)} N/m`
        ],

        [
            "Rigidez",
            `${formatarNumero(forcaPorMm, 3)} N/mm`
        ],

        [
            "Compressão",
            `${formatarNumero(compressaoMm, 2)} mm`
        ],

        [
            "Força da mola",
            `${formatarNumero(forcaN, 2)} N`
        ],

        [
            "Força da mola",
            `${formatarNumero(forcaKN, 3)} kN`
        ],

        [
            "Força da mola",
            `${formatarNumero(forcaTf, 4)} tf`
        ],

        [
            "Pressão equivalente",
            `${formatarNumero(pressaoBar, 3)} bar`
        ],

        [
            "Pressão equivalente",
            `${formatarNumero(pressaoMPa, 4)} MPa`
        ],

        [
            "Aumento de força por mm",
            `${formatarNumero(forcaPorMm, 3)} N/mm`
        ],

        [
            "Aumento de pressão por mm",
            `${formatarNumero(pressaoPorMm, 4)} bar/mm`
        ]

    ];

    resultadosDiretos.innerHTML =
        itens.map(
            ([rotulo, valor]) => `
                <div class="cartao-resultado">
                    <span>${rotulo}</span>
                    <strong>${valor}</strong>
                </div>
            `
        ).join("");

}

// ==========================================================
// CÁLCULO INVERSO
// ==========================================================

function calcularInverso() {

    const modo =
        selectModo.value;

    const pressaoBar =
        pressaoDesejadaInput.valueAsNumber;

    const forcaInformadaN =
        forcaDesejadaInput.valueAsNumber;

    const areaCm2 =
        areaInversaInput.valueAsNumber;

    const compressaoMm =
        compressaoInversaInput.valueAsNumber;

    if (
        !Number.isFinite(pressaoBar) ||
        !Number.isFinite(forcaInformadaN) ||
        !Number.isFinite(areaCm2) ||
        !Number.isFinite(compressaoMm) ||
        pressaoBar < 0 ||
        forcaInformadaN < 0 ||
        areaCm2 <= 0 ||
        compressaoMm <= 0
    ) {

        resultadosInversos.innerHTML = `
            <div class="aviso-erro">
                Preencha os campos com valores válidos.
            </div>
        `;

        return;
    }

    const compressaoM =
        compressaoMm / 1000;

    let forcaN;
    let pressaoEquivalenteBar;

    // --------------------------------------------------------
    // Pressão → força
    // --------------------------------------------------------

    if (modo === "pressao") {

        forcaN =
            10 *
            pressaoBar *
            areaCm2;

        pressaoEquivalenteBar =
            pressaoBar;

    }

    // --------------------------------------------------------
    // Força → pressão
    // --------------------------------------------------------

    else {

        forcaN =
            forcaInformadaN;

        pressaoEquivalenteBar =
            forcaN /
            (10 * areaCm2);

    }

    // --------------------------------------------------------
    // Rigidez
    //
    // K = F / x
    // --------------------------------------------------------

    const rigidezNm =
        forcaN /
        compressaoM;

    const rigidezNmm =
        rigidezNm /
        1000;

    const forcaKN =
        forcaN / 1000;

    const forcaTf =
        forcaN / 9806.65;

    // --------------------------------------------------------
    // Pressão por mm da mola calculada
    // --------------------------------------------------------

    const pressaoPorMm =
        rigidezNmm /
        (10 * areaCm2);

    // --------------------------------------------------------
    // Exibição
    // --------------------------------------------------------

    const itens = [

        [
            "Modo de cálculo",
            modo === "pressao"
                ? "Pressão → Rigidez"
                : "Força → Rigidez"
        ],

        [
            "Força necessária",
            `${formatarNumero(forcaN, 2)} N`
        ],

        [
            "Força necessária",
            `${formatarNumero(forcaKN, 3)} kN`
        ],

        [
            "Força necessária",
            `${formatarNumero(forcaTf, 4)} tf`
        ],

        [
            "Pressão equivalente",
            `${formatarNumero(
                pressaoEquivalenteBar,
                3
            )} bar`
        ],

        [
            "Compressão da mola",
            `${formatarNumero(
                compressaoMm,
                2
            )} mm`
        ],

        [
            "Rigidez necessária",
            `${formatarNumero(
                rigidezNm,
                2
            )} N/m`
        ],

        [
            "Rigidez necessária",
            `${formatarNumero(
                rigidezNmm,
                3
            )} N/mm`
        ],

        [
            "Pressão por mm de compressão",
            `${formatarNumero(
                pressaoPorMm,
                4
            )} bar/mm`
        ]

    ];

    resultadosInversos.innerHTML =
        itens.map(
            ([rotulo, valor]) => `
                <div class="cartao-resultado">
                    <span>${rotulo}</span>
                    <strong>${valor}</strong>
                </div>
            `
        ).join("");

}

// ==========================================================
// MOSTRAR / OCULTAR CAMPOS DO MODO INVERSO
// ==========================================================

function atualizarModo() {

    const modo =
        selectModo.value;

    const grupoPressao =
        pressaoDesejadaInput.closest(
            ".campo-calculadora"
        );

    const grupoForca =
        forcaDesejadaInput.closest(
            ".campo-calculadora"
        );

    if (modo === "pressao") {

        grupoPressao.style.display = "";

        grupoForca.style.display = "none";

    } else {

        grupoPressao.style.display = "none";

        grupoForca.style.display = "";

    }

    calcularInverso();

}

// ==========================================================
// EVENTOS
// ==========================================================

[
    rigidezInput,
    compressaoInput,
    areaInput
].forEach(input => {

    input.addEventListener(
        "input",
        calcularDireto
    );

});

[
    pressaoDesejadaInput,
    forcaDesejadaInput,
    areaInversaInput,
    compressaoInversaInput
].forEach(input => {

    input.addEventListener(
        "input",
        calcularInverso
    );

});

selectModo.addEventListener(
    "change",
    atualizarModo
);

// ==========================================================
// INICIALIZAÇÃO
// ==========================================================

calcularDireto();

atualizarModo();

// ==========================================================
// FÓRMULAS
// ==========================================================

const formulas =
    document.createElement("div");

formulas.className =
    "formulas-calculadora";

formulas.innerHTML = `

<h3>Fórmulas utilizadas</h3>

<div class="formula">
    F = K · x
</div>

<div class="formula">
    F [N] =
    K [N/m] · x [m]
</div>

<div class="formula">
    p [bar] =
    F [N] /
    (10 · A [cm²])
</div>

<div class="formula">
    F [N] =
    10 · p [bar] · A [cm²]
</div>

<div class="formula">
    K [N/m] =
    F [N] / x [m]
</div>

<div class="formula">
    K [N/mm] =
    K [N/m] / 1000
</div>

<div class="informacao">

    A compressão informada representa a deformação da mola
    em relação ao seu comprimento livre.

    <br><br>

    No cálculo direto:

    <br>

    rigidez → força → pressão equivalente

    <br><br>

    No cálculo inverso:

    <br>

    pressão → força → rigidez

    <br>

    ou

    <br>

    força → rigidez

    <br><br>

    A pressão equivalente depende da área hidráulica sobre
    a qual a força da mola está sendo representada.

    <br><br>

    Portanto, a mesma mola pode representar pressões
    diferentes quando aplicada sobre áreas diferentes.

</div>


`;

container.appendChild(formulas);
```
