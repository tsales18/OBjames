
```dataviewjs
// ============================================================
// VELOCIDADE DO CILINDRO COM CARGA
//
// Pressão exigida:
// p [bar] = F [N] / (10 × A [cm²])
//
// Vazão útil:
// Qútil = Qbomba − Qalívio − Qfugas
//
// Velocidade:
// v [mm/s] = Qútil × 10000 / (60 × A)
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
criarTitulo("Dados", 3);

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
    17000000,
    0,
    null,
    1000,
    "N"
);

const vazaoBombaInput = criarCampoNumero(
    "Vazão da bomba",
    245.4,
    0,
    null,
    1,
    "L/min"
);

const vazaoAlivioInput = criarCampoNumero(
    "Vazão desviada pelo alívio",
    190.1,
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

// ------------------------------------------------------------
// Cálculo
// ------------------------------------------------------------

function calcular() {
    const areaCm2 = Number(areaInput.value);
    const forcaN = Number(forcaInput.value);
    const vazaoBomba = Number(vazaoBombaInput.value);
    const vazaoAlivio = Number(vazaoAlivioInput.value);
    const vazaoFugas = Number(vazaoFugasInput.value);

    // Validação
    if (
        areaCm2 <= 0 ||
        forcaN < 0 ||
        vazaoBomba < 0 ||
        vazaoAlivio < 0 ||
        vazaoFugas < 0
    ) {
        resultados.innerHTML = `
            <div class="aviso-erro">
                A área deve ser maior que zero e os demais valores
                não podem ser negativos.
            </div>
        `;
        return;
    }

    // --------------------------------------------------------
    // Pressão exigida pela carga
    // --------------------------------------------------------

    const pressaoBar = forcaN / (10 * areaCm2);
    const pressaoMPa = pressaoBar / 10;

    // --------------------------------------------------------
    // Vazão útil
    // --------------------------------------------------------

    const vazaoDesviadaTotal =
        vazaoAlivio + vazaoFugas;

    const vazaoUtilCalculada =
        vazaoBomba - vazaoDesviadaTotal;

    // Impede vazão útil negativa
    const vazaoUtil =
        Math.max(vazaoUtilCalculada, 0);

    const aproveitamentoVazao =
        vazaoBomba > 0
            ? (vazaoUtil / vazaoBomba) * 100
            : 0;

    // --------------------------------------------------------
    // Velocidade
    // --------------------------------------------------------

    const velocidadeMmS =
        (vazaoUtil * 10000) /
        (60 * areaCm2);

    const velocidadeCmS = velocidadeMmS / 10;
    const velocidadeMS = velocidadeMmS / 1000;
    const velocidadeMMin = velocidadeMmS * 0.06;

    // Tempo para percorrer 1 metro
    const tempoPorMetroS =
        velocidadeMmS > 0
            ? 1000 / velocidadeMmS
            : 0;

    // --------------------------------------------------------
    // Exibição
    // --------------------------------------------------------

    resultados.innerHTML = `
        <div class="cartao-resultado">
            <span>Pressão exigida pela carga</span>
            <strong>
                ${formatarNumero(pressaoBar)} bar
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Pressão exigida</span>
            <strong>
                ${formatarNumero(pressaoMPa, 3)} MPa
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Vazão desviada total</span>
            <strong>
                ${formatarNumero(vazaoDesviadaTotal)} L/min
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Vazão útil para o cilindro</span>
            <strong>
                ${formatarNumero(vazaoUtil)} L/min
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Aproveitamento da vazão</span>
            <strong>
                ${formatarNumero(aproveitamentoVazao)} %
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Velocidade do cilindro</span>
            <strong>
                ${formatarNumero(velocidadeMmS, 3)} mm/s
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Velocidade do cilindro</span>
            <strong>
                ${formatarNumero(velocidadeCmS, 4)} cm/s
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
                ${formatarNumero(velocidadeMMin, 4)} m/min
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Tempo para percorrer 1 metro</span>
            <strong>
                ${
                    velocidadeMmS > 0
                        ? `${formatarNumero(tempoPorMetroS, 2)} s`
                        : "Sem movimento"
                }
            </strong>
        </div>
    `;

    if (vazaoUtilCalculada < 0) {
        resultados.innerHTML += `
            <div class="aviso-erro">
                A soma da vazão desviada pelo alívio com os vazamentos
                é maior que a vazão fornecida pela bomba. A vazão útil
                foi limitada a 0 L/min.
            </div>
        `;
    }
}

// ------------------------------------------------------------
// Atualização automática
// ------------------------------------------------------------

const entradas = [
    areaInput,
    forcaInput,
    vazaoBombaInput,
    vazaoAlivioInput,
    vazaoFugasInput
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
        p [bar] =
        F [N] /
        (10 · A [cm²])
    </div>

    <div class="formula">
        Q<sub>útil</sub> =
        Q<sub>bomba</sub> −
        Q<sub>alívio</sub> −
        Q<sub>fugas</sub>
    </div>

    <div class="formula">
        v [mm/s] =
        Q<sub>útil</sub> [L/min] · 10.000 /
        (60 · A [cm²])
    </div>

    <div class="formula">
        Aproveitamento (%) =
        100 · Q<sub>útil</sub> /
        Q<sub>bomba</sub>
    </div>

    <div class="informacao">
        A carga determina a pressão necessária para movimentar o
        cilindro, enquanto a vazão útil determina sua velocidade.
        A vazão pelo alívio precisa ser conhecida ou estimada a partir
        do comportamento real do circuito. Ela não pode ser determinada
        somente pela carga aplicada.
    </div>
`;

container.appendChild(formulas);
```