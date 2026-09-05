
```dataviewjs
// ==========================================================
// POTÊNCIA DE BOMBA HIDRÁULICA
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

criarTitulo("Potência da Bomba Hidráulica");

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
        valores[2] > 100 ||
        valores[3] <= 0 ||
        valores[3] > 100
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
            "Potência hidráulica",
            `${formatarNumero(potenciaHidraulicaKw)} kW`
        ],

        [
            "Potência hidráulica",
            `${formatarNumero(potenciaHidraulicaCv)} cv`
            
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
    η<sub>global</sub> =
    η<sub>bomba</sub> · η<sub>motor</sub>
</div>

<div class="formula">
    P<sub>perdas</sub> =
    P<sub>elétrica</sub> −
    P<sub>hidráulica</sub>
</div>

<div class="informacao">

    A potência hidráulica representa a potência efetivamente
    transferida ao óleo.

    <br><br>

    A potência no eixo considera as perdas da bomba.

    <br><br>

    A potência elétrica considera também as perdas do motor.

    <br><br>

    Para dimensionamento real do motor deve-se ainda considerar
    margem de segurança, regime de trabalho e condições indicadas
    pelos fabricantes da bomba e do motor.

</div>
`;

container.appendChild(formulas);
```
```
