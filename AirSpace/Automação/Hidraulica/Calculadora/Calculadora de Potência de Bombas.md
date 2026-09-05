
```dataviewjs
// ==========================================================
// POTÊNCIA E TORQUE DE BOMBA HIDRÁULICA
//
// Potência hidráulica:
// Ph [kW] = p [bar] × Q [L/min] / 600
//
// Potência requerida no eixo:
// Peixo = Ph / ηbomba
//
// Potência elétrica requerida:
// Pel = Peixo / ηmotor
//
// Torque hidráulico ideal:
// Th [N·m] = 9550 × Ph [kW] / n [rpm]
//
// Torque requerido no eixo:
// Teixo [N·m] = 9550 × Peixo [kW] / n [rpm]
//
// 1 kW = 1,35962 cv
// 1 kW = 1,34102 hp
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

criarTitulo("Potência e Torque da Bomba Hidráulica");

criarTitulo("Dados", 3);

const pressaoInput = criarCampoNumero(
    "Pressão de trabalho",
    260,
    0,
    null,
    1,
    "bar"
);

const vazaoInput = criarCampoNumero(
    "Vazão da bomba",
    310,
    0,
    null,
    1,
    "L/min"
);

const rotacaoInput = criarCampoNumero(
    "Rotação da bomba",
    1000,
    1,
    null,
    1,
    "rpm"
);

const eficienciaBombaInput = criarCampoNumero(
    "Eficiência total da bomba",
    90,
    1,
    100,
    1,
    "%"
);

const eficienciaMotorInput = criarCampoNumero(
    "Eficiência do motor",
    95,
    1,
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

const entradas = [
    pressaoInput,
    vazaoInput,
    rotacaoInput,
    eficienciaBombaInput,
    eficienciaMotorInput
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
        valores[1] < 0 ||
        valores[2] <= 0 ||
        valores[3] <= 0 ||
        valores[3] > 100 ||
        valores[4] <= 0 ||
        valores[4] > 100
    ) {

        resultados.innerHTML = `
            <div class="aviso-erro">
                Preencha os campos com valores válidos.
            </div>
        `;

        return;
    }

    const [
        pressaoBar,
        vazaoLmin,
        rotacaoRpm,
        eficienciaBombaPercentual,
        eficienciaMotorPercentual
    ] = valores;

    // --------------------------------------------------------
    // Eficiências
    // --------------------------------------------------------

    const eficienciaBomba =
        eficienciaBombaPercentual / 100;

    const eficienciaMotor =
        eficienciaMotorPercentual / 100;

    // --------------------------------------------------------
    // Potência hidráulica
    // --------------------------------------------------------

    const potenciaHidraulicaKw =
        (pressaoBar * vazaoLmin) / 600;

    // --------------------------------------------------------
    // Potência mecânica necessária no eixo
    // --------------------------------------------------------

    const potenciaEixoKw =
        potenciaHidraulicaKw / eficienciaBomba;

    // --------------------------------------------------------
    // Potência elétrica necessária
    // --------------------------------------------------------

    const potenciaEletricaKw =
        potenciaEixoKw / eficienciaMotor;

    // --------------------------------------------------------
    // Torque
    //
    // T [N·m] = 9550 × P [kW] / n [rpm]
    // --------------------------------------------------------

    const torqueHidraulicoNm =
        (9550 * potenciaHidraulicaKw) /
        rotacaoRpm;

    const torqueEixoNm =
        (9550 * potenciaEixoKw) /
        rotacaoRpm;

    const torqueExtraPerdasNm =
        torqueEixoNm -
        torqueHidraulicoNm;

    // --------------------------------------------------------
    // Cilindrada ideal correspondente à vazão e RPM
    //
    // Vg [cm³/rev] = Q [L/min] × 1000 / n [rpm]
    //
    // Valor ideal, sem corrigir eficiência volumétrica.
    // --------------------------------------------------------

    const cilindradaIdealCm3Rev =
        (vazaoLmin * 1000) /
        rotacaoRpm;

    // --------------------------------------------------------
    // Conversões
    // --------------------------------------------------------

    const potenciaHidraulicaCv =
        potenciaHidraulicaKw * 1.35962;

    const potenciaEixoCv =
        potenciaEixoKw * 1.35962;

    const potenciaEletricaCv =
        potenciaEletricaKw * 1.35962;

    const potenciaHidraulicaHp =
        potenciaHidraulicaKw * 1.34102;

    const potenciaEletricaHp =
        potenciaEletricaKw * 1.34102;

    // --------------------------------------------------------
    // Perdas
    // --------------------------------------------------------

    const perdasBombaKw =
        potenciaEixoKw -
        potenciaHidraulicaKw;

    const perdasMotorKw =
        potenciaEletricaKw -
        potenciaEixoKw;

    const perdasTotaisKw =
        potenciaEletricaKw -
        potenciaHidraulicaKw;

    // --------------------------------------------------------
    // Eficiência global
    // --------------------------------------------------------

    const eficienciaGlobal =
        eficienciaBomba *
        eficienciaMotor *
        100;

    // --------------------------------------------------------
    // Exibição
    // --------------------------------------------------------

    const itens = [

        [
            "Pressão",
            `${formatarNumero(pressaoBar)} bar`
        ],

        [
            "Vazão",
            `${formatarNumero(vazaoLmin)} L/min`
        ],

        [
            "Rotação",
            `${formatarNumero(rotacaoRpm, 0)} rpm`
        ],

        [
            "Cilindrada ideal correspondente",
            `${formatarNumero(cilindradaIdealCm3Rev)} cm³/rev`
        ],

        [
            "Potência hidráulica",
            `${formatarNumero(potenciaHidraulicaKw)} kW`
        ],

        [
            "Potência hidráulica",
            `${formatarNumero(potenciaHidraulicaCv)} cv`
        ],

        [
            "Torque hidráulico ideal",
            `${formatarNumero(torqueHidraulicoNm)} N·m`
        ],

        [
            "Potência requerida no eixo",
            `${formatarNumero(potenciaEixoKw)} kW`
        ],

        [
            "Potência requerida no eixo",
            `${formatarNumero(potenciaEixoCv)} cv`
        ],

        [
            "Torque requerido no eixo",
            `${formatarNumero(torqueEixoNm)} N·m`
        ],

        [
            "Torque adicional devido às perdas",
            `${formatarNumero(torqueExtraPerdasNm)} N·m`
        ],

        [
            "Potência elétrica requerida",
            `${formatarNumero(potenciaEletricaKw)} kW`
        ],

        [
            "Potência elétrica requerida",
            `${formatarNumero(potenciaEletricaCv)} cv`
        ],

        [
            "Potência elétrica requerida",
            `${formatarNumero(potenciaEletricaHp)} hp`
        ],

        [
            "Perdas estimadas na bomba",
            `${formatarNumero(perdasBombaKw)} kW`
        ],

        [
            "Perdas estimadas no motor",
            `${formatarNumero(perdasMotorKw)} kW`
        ],

        [
            "Perdas totais",
            `${formatarNumero(perdasTotaisKw)} kW`
        ],

        [
            "Eficiência global",
            `${formatarNumero(eficienciaGlobal)} %`
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

const formulas = document.createElement("div");

formulas.className = "formulas-calculadora";

formulas.innerHTML = `

<h3>Fórmulas utilizadas</h3>

<div class="formula">
    P<sub>hid</sub> [kW] =
    p [bar] · Q [L/min] / 600
</div>

<div class="formula">
    P<sub>eixo</sub> =
    P<sub>hid</sub> / η<sub>bomba</sub>
</div>

<div class="formula">
    P<sub>elétrica</sub> =
    P<sub>eixo</sub> / η<sub>motor</sub>
</div>

<div class="formula">
    T<sub>hid</sub> [N·m] =
    9550 · P<sub>hid</sub> [kW] /
    n [rpm]
</div>

<div class="formula">
    T<sub>eixo</sub> [N·m] =
    9550 · P<sub>eixo</sub> [kW] /
    n [rpm]
</div>

<div class="formula">
    V<sub>g ideal</sub> [cm³/rev] =
    Q [L/min] · 1000 /
    n [rpm]
</div>

<div class="formula">
    η<sub>global</sub> =
    η<sub>bomba</sub> · η<sub>motor</sub>
</div>

<div class="formula">
    P<sub>perdas</sub> =
    P<sub>elétrica</sub> −
    P<sub>hidráulica</sub>
</div>

<div class="informacao">

    O torque hidráulico é o torque ideal correspondente
    à potência transferida ao óleo.

    <br><br>

    O torque requerido no eixo considera as perdas da bomba
    e representa o torque que o motor deve fornecer ao eixo
    da bomba na rotação informada.

    <br><br>

    Quanto maior a pressão, maior será o torque requerido
    para manter a mesma cilindrada.

    <br><br>

    Se o motor não conseguir fornecer esse torque, sua
    rotação poderá cair. Como a vazão depende da rotação,
    a consequência será:

    <br><br>

    pressão ↑ → torque requerido ↑ → RPM ↓ → vazão ↓

    <br><br>

    A cilindrada exibida é calculada apenas pela relação
    ideal entre vazão e rotação, sem correção pela
    eficiência volumétrica.

</div>

`;

container.appendChild(formulas);
```
```
