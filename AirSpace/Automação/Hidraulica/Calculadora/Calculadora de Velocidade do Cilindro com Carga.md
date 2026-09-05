

```dataviewjs
// ==========================================================
// VELOCIDADE DO CILINDRO COM CARGA EM TONELADAS-FORÇA
// CONSIDERANDO LIMITE DE PRESSÃO E POTÊNCIA DA BOMBA
//
// F [N] = F [tf] × 9806,65
//
// p [bar] = F [N] / (10 × A [cm²])
//
// Phid,max = Pmotor × η
//
// Qpotência [L/min] =
// 600 × Phid,max [kW] / p [bar]
//
// Qdisponível = min(Qnominal, Qpotência)
//
// Qútil = Qdisponível − Qalívio − Qfugas
//
// v [mm/s] =
// Qútil × 10000 / (60 × A [cm²])
//
// Se pexigida > pmax:
// cilindro não vence a carga → v = 0
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

// ------------------------------------------------------------
// Entradas
// ------------------------------------------------------------

criarTitulo("Velocidade do Cilindro com Carga");

criarTitulo("Cilindro e carga", 3);

const areaInput = criarCampoNumero(
    "Área efetiva do cilindro",
    6310,
    0.01,
    null,
    1,
    "cm²"
);

const forcaInput = criarCampoNumero(
    "Carga aplicada",
    1500,
    0,
    null,
    1,
    "tf"
);

// ------------------------------------------------------------
// Dados da bomba
// ------------------------------------------------------------

criarTitulo("Bomba / Grupo de Bombas", 3);

const vazaoBombaInput = criarCampoNumero(
    "Vazão nominal máxima",
    310,
    0,
    null,
    1,
    "L/min"
);

const pressaoMaximaInput = criarCampoNumero(
    "Pressão máxima disponível",
    260,
    0.01,
    null,
    1,
    "bar"
);

const potenciaMotorInput = criarCampoNumero(
    "Potência disponível no acionamento",
    150,
    0,
    null,
    1,
    "kW"
);

const eficienciaInput = criarCampoNumero(
    "Eficiência global motor + bomba",
    90,
    1,
    100,
    1,
    "%"
);

// ------------------------------------------------------------
// Perdas de vazão
// ------------------------------------------------------------

criarTitulo("Perdas de vazão", 3);

const vazaoAlivioInput = criarCampoNumero(
    "Vazão desviada pelo alívio",
    0,
    0,
    null,
    1,
    "L/min"
);

const vazaoFugasInput = criarCampoNumero(
    "Vazamentos",
    0,
    0,
    null,
    0.1,
    "L/min"
);

// ------------------------------------------------------------
// Resultados
// ------------------------------------------------------------

criarTitulo("Resultados", 3);

const resultados = document.createElement("div");

resultados.className = "resultados-calculadora";

container.appendChild(resultados);

const entradas = [
    areaInput,
    forcaInput,
    vazaoBombaInput,
    pressaoMaximaInput,
    potenciaMotorInput,
    eficienciaInput,
    vazaoAlivioInput,
    vazaoFugasInput
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
        valores[0] <= 0 ||
        valores[2] < 0 ||
        valores[3] <= 0 ||
        valores[4] < 0 ||
        valores[5] <= 0 ||
        valores[5] > 100 ||
        valores[6] < 0 ||
        valores[7] < 0
    ) {

        resultados.innerHTML = `
            <div class="aviso-erro">
                Preencha todos os campos com valores válidos.
            </div>
        `;

        return;
    }

    const [
        areaCm2,
        forcaTf,
        vazaoNominal,
        pressaoMaxima,
        potenciaMotor,
        eficienciaPercentual,
        vazaoAlivio,
        vazaoFugas
    ] = valores;

    // --------------------------------------------------------
    // Força
    // --------------------------------------------------------

    const forcaN =
        forcaTf * 9806.65;

    // --------------------------------------------------------
    // Pressão necessária para vencer a carga
    // --------------------------------------------------------

    const pressaoExigida =
        forcaN / (10 * areaCm2);

    const pressaoMPa =
        pressaoExigida / 10;

    // --------------------------------------------------------
    // Potência hidráulica máxima disponível
    // --------------------------------------------------------

    const eficiencia =
        eficienciaPercentual / 100;

    const potenciaHidraulicaMax =
        potenciaMotor * eficiencia;

    // --------------------------------------------------------
    // Verifica se a bomba consegue vencer a carga
    // --------------------------------------------------------

    const cargaPodeSerVencida =
        pressaoExigida <= pressaoMaxima;

    // --------------------------------------------------------
    // Vazão máxima permitida pela potência
    // --------------------------------------------------------

    let vazaoLimitadaPotencia;

    if (pressaoExigida > 0) {

        vazaoLimitadaPotencia =
            (
                600 *
                potenciaHidraulicaMax
            ) /
            pressaoExigida;

    } else {

        vazaoLimitadaPotencia =
            vazaoNominal;

    }

    // --------------------------------------------------------
    // Vazão disponível da bomba
    //
    // A bomba não pode ultrapassar:
    // - sua vazão nominal
    // - sua potência máxima
    // --------------------------------------------------------

    let vazaoDisponivel =
        Math.min(
            vazaoNominal,
            vazaoLimitadaPotencia
        );

    if (!cargaPodeSerVencida) {

        vazaoDisponivel = 0;

    }

    // --------------------------------------------------------
    // Vazão útil
    // --------------------------------------------------------

    const vazaoDesviadaTotal =
        vazaoAlivio +
        vazaoFugas;

    const vazaoUtilCalculada =
        vazaoDisponivel -
        vazaoDesviadaTotal;

    const vazaoUtil =
        Math.max(
            vazaoUtilCalculada,
            0
        );

    // --------------------------------------------------------
    // Aproveitamento da vazão
    // --------------------------------------------------------

    const aproveitamentoVazao =
        vazaoNominal > 0
            ? (
                vazaoUtil /
                vazaoNominal
            ) * 100
            : 0;

    // --------------------------------------------------------
    // Percentual da capacidade de vazão
    // --------------------------------------------------------

    const percentualVazaoDisponivel =
        vazaoNominal > 0
            ? (
                vazaoDisponivel /
                vazaoNominal
            ) * 100
            : 0;

    // --------------------------------------------------------
    // Velocidade
    // --------------------------------------------------------

    const velocidadeMmS =
        cargaPodeSerVencida
            ? (
                vazaoUtil * 10000
            ) /
            (
                60 * areaCm2
            )
            : 0;

    const velocidadeCmS =
        velocidadeMmS / 10;

    const velocidadeMS =
        velocidadeMmS / 1000;

    const velocidadeMMin =
        velocidadeMmS * 0.06;

    // --------------------------------------------------------
    // Tempo para 1 metro
    // --------------------------------------------------------

    const tempoPorMetro =
        velocidadeMmS > 0
            ? `${formatarNumero(
                1000 / velocidadeMmS
            )} s`
            : "Sem movimento";

    // --------------------------------------------------------
    // Potência hidráulica realmente exigida
    // --------------------------------------------------------

    const potenciaHidraulicaAtual =
        (
            pressaoExigida *
            vazaoDisponivel
        ) / 600;

    // --------------------------------------------------------
    // Percentual de potência utilizada
    // --------------------------------------------------------

    const percentualPotencia =
        potenciaHidraulicaMax > 0
            ? (
                potenciaHidraulicaAtual /
                potenciaHidraulicaMax
            ) * 100
            : 0;

    // --------------------------------------------------------
    // Limitação atual
    // --------------------------------------------------------

    let modoOperacao = "";

    if (!cargaPodeSerVencida) {

        modoOperacao =
            "Carga acima da capacidade de pressão";

    } else if (
        vazaoLimitadaPotencia <
        vazaoNominal
    ) {

        modoOperacao =
            "Vazão limitada pela potência";

    } else {

        modoOperacao =
            "Vazão limitada pela capacidade nominal da bomba";

    }

    // --------------------------------------------------------
    // Exibição
    // --------------------------------------------------------

    const itens = [

        [
            "Carga aplicada",
            `${formatarNumero(
                forcaTf
            )} tf`
        ],

        [
            "Carga convertida",
            `${formatarNumero(
                forcaN,
                0
            )} N`
        ],

        [
            "Pressão exigida pela carga",
            `${formatarNumero(
                pressaoExigida
            )} bar`
        ],

        [
            "Pressão exigida",
            `${formatarNumero(
                pressaoMPa,
                3
            )} MPa`
        ],

        [
            "Pressão máxima disponível",
            `${formatarNumero(
                pressaoMaxima
            )} bar`
        ],

        [
            "Potência hidráulica disponível",
            `${formatarNumero(
                potenciaHidraulicaMax
            )} kW`
        ],

        [
            "Vazão nominal da bomba",
            `${formatarNumero(
                vazaoNominal
            )} L/min`
        ],

        [
            "Vazão máxima permitida pela potência",
            `${formatarNumero(
                vazaoLimitadaPotencia
            )} L/min`
        ],

        [
            "Vazão realmente disponível",
            `${formatarNumero(
                vazaoDisponivel
            )} L/min`
        ],

        [
            "Capacidade de vazão disponível",
            `${formatarNumero(
                percentualVazaoDisponivel
            )} %`
        ],

        [
            "Vazão desviada total",
            `${formatarNumero(
                vazaoDesviadaTotal
            )} L/min`
        ],

        [
            "Vazão útil para o cilindro",
            `${formatarNumero(
                vazaoUtil
            )} L/min`
        ],

        [
            "Aproveitamento da vazão nominal",
            `${formatarNumero(
                aproveitamentoVazao
            )} %`
        ],

        [
            "Potência hidráulica utilizada",
            `${formatarNumero(
                potenciaHidraulicaAtual
            )} kW`
        ],

        [
            "Utilização da potência disponível",
            `${formatarNumero(
                percentualPotencia
            )} %`
        ],

        [
            "Velocidade do cilindro",
            `${formatarNumero(
                velocidadeMmS,
                3
            )} mm/s`
        ],

        [
            "Velocidade do cilindro",
            `${formatarNumero(
                velocidadeCmS,
                4
            )} cm/s`
        ],

        [
            "Velocidade SI",
            `${formatarNumero(
                velocidadeMS,
                6
            )} m/s`
        ],

        [
            "Velocidade por minuto",
            `${formatarNumero(
                velocidadeMMin,
                4
            )} m/min`
        ],

        [
            "Tempo para percorrer 1 metro",
            tempoPorMetro
        ],

        [
            "Condição de operação",
            modoOperacao
        ]

    ];

    resultados.innerHTML =
        itens
        .map(
            ([rotulo, valor]) => `
                <div class="cartao-resultado">
                    <span>${rotulo}</span>
                    <strong>${valor}</strong>
                </div>
            `
        )
        .join("");

    // --------------------------------------------------------
    // Avisos
    // --------------------------------------------------------

    if (!cargaPodeSerVencida) {

        resultados.innerHTML += `
            <div class="aviso-erro">
                A carga exige
                ${formatarNumero(
                    pressaoExigida
                )} bar,
                mas o sistema está limitado a
                ${formatarNumero(
                    pressaoMaxima
                )} bar.

                O cilindro não consegue vencer essa carga.
                A velocidade foi considerada 0 mm/s.
            </div>
        `;

    }

    if (
        cargaPodeSerVencida &&
        vazaoLimitadaPotencia <
        vazaoNominal
    ) {

        resultados.innerHTML += `
            <div class="informacao">
                A alta pressão está limitando a vazão.

                A bomba possui capacidade nominal de
                ${formatarNumero(
                    vazaoNominal
                )} L/min,
                porém a potência disponível permite apenas
                ${formatarNumero(
                    vazaoLimitadaPotencia
                )} L/min
                nessa pressão.
            </div>
        `;

    }

    if (vazaoUtilCalculada < 0) {

        resultados.innerHTML += `
            <div class="aviso-erro">
                Alívio e vazamentos ultrapassam a vazão
                disponível da bomba.

                A vazão útil foi limitada a 0 L/min.
            </div>
        `;

    }

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
    F [N] =
    F [tf] · 9.806,65
</div>

<div class="formula">
    p [bar] =
    F [N] /
    (10 · A [cm²])
</div>

<div class="formula">
    P<sub>hid,max</sub> =
    P<sub>motor</sub> · η
</div>

<div class="formula">
    Q<sub>potência</sub> [L/min] =
    600 · P<sub>hid,max</sub> [kW] /
    p [bar]
</div>

<div class="formula">
    Q<sub>disponível</sub> =
    min(
        Q<sub>nominal</sub>,
        Q<sub>potência</sub>
    )
</div>

<div class="formula">
    Q<sub>útil</sub> =
    Q<sub>disponível</sub>
    − Q<sub>alívio</sub>
    − Q<sub>fugas</sub>
</div>

<div class="formula">
    v [mm/s] =
    Q<sub>útil</sub> [L/min] · 10.000 /
    (60 · A [cm²])
</div>

<div class="formula">
    P<sub>hid</sub> [kW] =
    p [bar] · Q [L/min] / 600
</div>

<div class="informacao">

    Nesta versão a carga aumenta a pressão exigida pelo
    cilindro.

    Enquanto a potência disponível for suficiente,
    a bomba consegue manter sua vazão nominal.

    Quando a pressão aumenta a ponto de exigir mais
    potência do que o acionamento possui, a vazão
    disponível passa a ser limitada por:

    <br><br>

    Q = 600 · P / p

    <br><br>

    Portanto:

    <br>

    pressão ↑ → vazão disponível ↓ → velocidade ↓

    <br><br>

    Se a pressão exigida pela carga superar a pressão
    máxima configurada, considera-se que o cilindro
    não consegue vencer a carga e sua velocidade
    passa a ser 0 mm/s.

</div>

`;

container.appendChild(formulas);
```
```