
```dataviewjs
// ==========================================================
// VELOCIDADE E QUEDA DE PRESSÃO EM CARTUCHO / ORIFÍCIO
//
// Velocidade:
//
// v = Q / A
//
// Para seção circular:
//
// A = π · D² / 4
//
// Queda de pressão:
//
// Δp = ρ / 2 · (v / Cd)²
//
// Ou:
//
// Q = Cd · A · √(2 · Δp / ρ)
//
// ==========================================================

const container = dv.el("div", "");

container.className = "calculadora-orificio";

// ------------------------------------------------------------
// Funções
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

// ------------------------------------------------------------
// Título
// ------------------------------------------------------------

criarTitulo(
    "Velocidade e Queda de Pressão em Cartucho"
);

criarTitulo("Dados", 3);

// ------------------------------------------------------------
// Vazão
// ------------------------------------------------------------

const vazaoInput = criarCampoNumero(
    "Vazão através do cartucho",
    245,
    0,
    null,
    1,
    "L/min"
);

// ------------------------------------------------------------
// Área efetiva
// ------------------------------------------------------------

const areaInput = criarCampoNumero(
    "Área efetiva de passagem",
    200,
    0.01,
    null,
    1,
    "mm²"
);

// ------------------------------------------------------------
// Diâmetro equivalente
//
// Apenas para comparação com uma passagem circular.
// ------------------------------------------------------------

const diametroInput = criarCampoNumero(
    "Diâmetro circular equivalente",
    16,
    0.01,
    null,
    0.1,
    "mm"
);

// ------------------------------------------------------------
// Coeficiente de descarga
// ------------------------------------------------------------

const cdInput = criarCampoNumero(
    "Coeficiente de descarga Cd",
    0.65,
    0.01,
    1,
    0.01,
    ""
);

// ------------------------------------------------------------
// Densidade
// ------------------------------------------------------------

const densidadeInput = criarCampoNumero(
    "Densidade do óleo",
    850,
    1,
    null,
    10,
    "kg/m³"
);

// ------------------------------------------------------------
// Resultados
// ------------------------------------------------------------

criarTitulo("Resultados", 3);

const resultados = document.createElement("div");

resultados.className = "resultados-calculadora";

container.appendChild(resultados);

// ------------------------------------------------------------
// Entradas
// ------------------------------------------------------------

const entradas = [
    vazaoInput,
    areaInput,
    diametroInput,
    cdInput,
    densidadeInput
];

// ------------------------------------------------------------
// Cálculo
// ------------------------------------------------------------

function calcular() {

    const valores = entradas.map(
        entrada => entrada.valueAsNumber
    );

    if (
        valores.some(valor => !Number.isFinite(valor)) ||
        valores[0] < 0 ||
        valores[1] <= 0 ||
        valores[2] <= 0 ||
        valores[3] <= 0 ||
        valores[3] > 1 ||
        valores[4] <= 0
    ) {

        resultados.innerHTML = `
            <div class="aviso-erro">
                Preencha todos os campos com valores válidos.
            </div>
        `;

        return;
    }

    const [
        vazaoLmin,
        areaMm2,
        diametroMm,
        cd,
        densidade
    ] = valores;

    // --------------------------------------------------------
    // Conversão da vazão
    //
    // L/min → m³/s
    // --------------------------------------------------------

    const vazaoM3s =
        vazaoLmin / 60000;

    // --------------------------------------------------------
    // Área efetiva informada
    //
    // mm² → m²
    // --------------------------------------------------------

    const areaM2 =
        areaMm2 / 1_000_000;

    // --------------------------------------------------------
    // Velocidade usando área efetiva
    // --------------------------------------------------------

    const velocidadeArea =
        vazaoM3s / areaM2;

    // --------------------------------------------------------
    // Queda de pressão usando área efetiva
    // --------------------------------------------------------

    const deltaPPaArea =
        (
            densidade / 2
        ) *
        (
            velocidadeArea / cd
        ) ** 2;

    const deltaPBarArea =
        deltaPPaArea / 100000;

    // --------------------------------------------------------
    // Área circular pelo diâmetro
    // --------------------------------------------------------

    const diametroM =
        diametroMm / 1000;

    const areaCircularM2 =
        Math.PI *
        diametroM ** 2 /
        4;

    const areaCircularMm2 =
        areaCircularM2 *
        1_000_000;

    // --------------------------------------------------------
    // Velocidade pela seção circular
    // --------------------------------------------------------

    const velocidadeCircular =
        vazaoM3s /
        areaCircularM2;

    // --------------------------------------------------------
    // Queda de pressão pela seção circular
    // --------------------------------------------------------

    const deltaPPaCircular =
        (
            densidade / 2
        ) *
        (
            velocidadeCircular / cd
        ) ** 2;

    const deltaPBarCircular =
        deltaPPaCircular / 100000;

    // --------------------------------------------------------
    // Diâmetro equivalente da área efetiva informada
    // --------------------------------------------------------

    const diametroEquivalenteM =
        Math.sqrt(
            4 * areaM2 / Math.PI
        );

    const diametroEquivalenteMm =
        diametroEquivalenteM * 1000;

    // --------------------------------------------------------
    // Exibição
    // --------------------------------------------------------

    const itens = [

        [
            "Vazão",
            `${formatarNumero(vazaoLmin)} L/min`
        ],

        [
            "Área efetiva informada",
            `${formatarNumero(areaMm2)} mm²`
        ],

        [
            "Diâmetro equivalente da área",
            `${formatarNumero(
                diametroEquivalenteMm,
                3
            )} mm`
        ],

        [
            "Velocidade pela área efetiva",
            `${formatarNumero(
                velocidadeArea,
                3
            )} m/s`
        ],

        [
            "Δp pela área efetiva",
            `${formatarNumero(
                deltaPBarArea,
                3
            )} bar`
        ],

        [
            "Diâmetro circular informado",
            `${formatarNumero(
                diametroMm
            )} mm`
        ],

        [
            "Área da seção circular",
            `${formatarNumero(
                areaCircularMm2,
                2
            )} mm²`
        ],

        [
            "Velocidade pela seção circular",
            `${formatarNumero(
                velocidadeCircular,
                3
            )} m/s`
        ],

        [
            "Δp pela seção circular",
            `${formatarNumero(
                deltaPBarCircular,
                3
            )} bar`
        ]

    ];

    resultados.innerHTML =
        itens.map(
            ([rotulo, valor]) => `
                <div class="cartao-resultado">
                    <span>${rotulo}</span>
                    <strong>${valor}</strong>
                </div>
            `
        ).join("");
}

// ------------------------------------------------------------
// Atualização automática
// ------------------------------------------------------------

entradas.forEach(entrada => {

    entrada.addEventListener(
        "input",
        calcular
    );

});

calcular();

// ------------------------------------------------------------
// Fórmulas
// ------------------------------------------------------------

const formulas =
    document.createElement("div");

formulas.className =
    "formulas-calculadora";

formulas.innerHTML = `

<h3>Fórmulas utilizadas</h3>

<div class="formula">
    Q [m³/s] =
    Q [L/min] / 60.000
</div>

<div class="formula">
    A [m²] =
    A [mm²] / 1.000.000
</div>

<div class="formula">
    v [m/s] =
    Q [m³/s] / A [m²]
</div>

<div class="formula">
    A<sub>circular</sub> =
    π · D² / 4
</div>

<div class="formula">
    Δp [Pa] =
    ρ / 2 · (v / C<sub>d</sub>)²
</div>

<div class="formula">
    Δp [bar] =
    Δp [Pa] / 100.000
</div>

<div class="formula">
    D<sub>equivalente</sub> =
    √(4 · A / π)
</div>

<div class="informacao">

    Para tubos e giclês circulares pode ser utilizado
    diretamente o diâmetro interno.

    <br><br>

    Para válvulas de cartucho, o mais correto é informar
    a área efetiva de passagem do elemento aberto.

    <br><br>

    O diâmetro nominal do cartucho não representa
    necessariamente a área disponível para o fluxo.

    <br><br>

    Em um cartucho:

    <br><br>

    vazão ↑ → velocidade ↑ → Δp ↑

    <br><br>

    e, aproximadamente:

    <br>

    Δp ∝ Q²

    <br><br>

    O cálculo de Δp utiliza a equação de orifício e deve
    ser considerado uma estimativa. A curva real do cartucho
    fornecida pelo fabricante é preferível quando disponível.

</div>

`;

container.appendChild(formulas);
```

