

```dataviewjs
// ============================================================
// CALCULADORA DE CARGA DO CILINDRO
//
// Percentual = Is_Fd / Escala máxima
// Fd = Fmáx × percentual
// Área = π × D² / 4
// p [bar] = Fd [N] / (10 × A [cm²])
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
// Título e entradas
// ------------------------------------------------------------

criarTitulo("Calculadora de Carga do Cilindro");
criarTitulo("Dados", 3);

const forcaMaximaInput = criarCampoNumero(
    "Força máxima do sistema",
    17651970,
    0,
    null,
    1000,
    "N"
);

const sinalFdInput = criarCampoNumero(
    "Sinal Is_Fd",
    5000,
    0,
    20000,
    100,
    "pontos"
);

const escalaMaximaInput = criarCampoNumero(
    "Escala máxima do sinal",
    20000,
    1,
    null,
    1000,
    "pontos"
);

const diametroInput = criarCampoNumero(
    "Diâmetro interno do cilindro",
    856,
    0.1,
    null,
    1,
    "mm"
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
    const FmaxN = Number(forcaMaximaInput.value);
    const sinalFd = Number(sinalFdInput.value);
    const escalaMaxima = Number(escalaMaximaInput.value);
    const diametroMm = Number(diametroInput.value);

    // Validação
    if (
        FmaxN < 0 ||
        sinalFd < 0 ||
        escalaMaxima <= 0 ||
        diametroMm <= 0
    ) {
        resultados.innerHTML = `
            <div class="aviso-erro">
                Verifique os valores informados. A escala máxima e o
                diâmetro devem ser maiores que zero. A força e o sinal
                não podem ser negativos.
            </div>
        `;
        return;
    }

    // --------------------------------------------------------
    // Área do cilindro
    // --------------------------------------------------------

    const diametroCm = diametroMm / 10;
    const areaCm2 =
        Math.PI * Math.pow(diametroCm, 2) / 4;

    // Área em m²
    const areaM2 = areaCm2 * 1e-4;

    // --------------------------------------------------------
    // Percentual da carga
    // --------------------------------------------------------

    const percentual = sinalFd / escalaMaxima;
    const percentualExibicao = percentual * 100;

    // --------------------------------------------------------
    // Força equivalente
    // --------------------------------------------------------

    const FdN = FmaxN * percentual;
    const FdKN = FdN / 1000;
    const FdMN = FdN / 1_000_000;
    const FdTf = FdN / 9806.65;

    // --------------------------------------------------------
    // Pressão equivalente
    // --------------------------------------------------------

    const pressaoBar = FdN / (10 * areaCm2);
    const pressaoMPa = pressaoBar / 10;

    // --------------------------------------------------------
    // Exibição
    // --------------------------------------------------------

    resultados.innerHTML = `
        <div class="cartao-resultado">
            <span>Carga utilizada</span>
            <strong>
                ${formatarNumero(percentualExibicao)} %
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Área efetiva do cilindro</span>
            <strong>
                ${formatarNumero(areaCm2)} cm²
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Área em unidade SI</span>
            <strong>
                ${formatarNumero(areaM2, 6)} m²
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Força equivalente</span>
            <strong>
                ${formatarNumero(FdN, 0)} N
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Força equivalente</span>
            <strong>
                ${formatarNumero(FdKN)} kN
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Força equivalente</span>
            <strong>
                ${formatarNumero(FdMN, 3)} MN
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Força equivalente</span>
            <strong>
                ${formatarNumero(FdTf)} tf
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Pressão teórica equivalente</span>
            <strong>
                ${formatarNumero(pressaoBar)} bar
            </strong>
        </div>

        <div class="cartao-resultado">
            <span>Pressão equivalente</span>
            <strong>
                ${formatarNumero(pressaoMPa, 3)} MPa
            </strong>
        </div>
    `;

    // Alerta se o sinal ultrapassar a escala configurada
    if (sinalFd > escalaMaxima) {
        resultados.innerHTML += `
            <div class="aviso-erro">
                O sinal Is_Fd está acima da escala máxima configurada.
                Por isso, a carga calculada ultrapassa 100% da força
                máxima do sistema.
            </div>
        `;
    }
}

// ------------------------------------------------------------
// Atualização automática
// ------------------------------------------------------------

const entradas = [
    forcaMaximaInput,
    sinalFdInput,
    escalaMaximaInput,
    diametroInput
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
        Carga (%) =
        100 · Is<sub>Fd</sub> /
        Escala<sub>máxima</sub>
    </div>

    <div class="formula">
        F<sub>d</sub> =
        F<sub>máx</sub> ·
        Is<sub>Fd</sub> /
        Escala<sub>máxima</sub>
    </div>

    <div class="formula">
        A =
        π · D² / 4
    </div>

    <div class="formula">
        p [bar] =
        F<sub>d</sub> [N] /
        (10 · A [cm²])
    </div>

    <div class="informacao">
        A pressão calculada é a pressão hidráulica teórica necessária
        para produzir a força indicada, considerando apenas a área
        circular do cilindro. Atrito das vedações, contrapressão e
        perdas mecânicas não estão incluídos.
    </div>
`;

container.appendChild(formulas);
```
