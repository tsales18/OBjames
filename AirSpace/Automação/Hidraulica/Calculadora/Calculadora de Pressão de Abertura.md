
```dataviewjs
// ============================================================
// CALCULADORA DE PRESSÃO NECESSÁRIA PARA ABRIR A VÁLVULA
//
// Força hidráulica:
// F = p × A
//
// Força da mola:
// Fm = K × x₀
//
// Conversões:
// 1 bar = 100.000 Pa
// 1 cm² = 0,0001 m²
// 1 mm = 0,001 m
// ============================================================

const container = dv.el("div", "");
container.className = "calculadora-orificio";

// ------------------------------------------------------------
// Título
// ------------------------------------------------------------

const titulo = document.createElement("h2");
titulo.textContent =
    "Calculadora de Pressão Necessária para Abrir a Válvula";

container.appendChild(titulo);

// ------------------------------------------------------------
// Funções para criar os campos
// ------------------------------------------------------------

function criarTituloSecao(texto) {
    const tituloSecao = document.createElement("h3");
    tituloSecao.textContent = texto;
    container.appendChild(tituloSecao);
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

function criarSelecao(rotulo, opcoes) {
    const grupo = document.createElement("div");
    grupo.className = "campo-calculadora";

    const label = document.createElement("label");
    label.textContent = rotulo;

    const linha = document.createElement("div");
    linha.className = "linha-entrada";

    const select = document.createElement("select");

    for (const opcao of opcoes) {
        const item = document.createElement("option");
        item.value = opcao;
        item.textContent = opcao;
        select.appendChild(item);
    }

    linha.appendChild(select);
    grupo.appendChild(label);
    grupo.appendChild(linha);
    container.appendChild(grupo);

    return select;
}

// ------------------------------------------------------------
// Lado que deve abrir
// ------------------------------------------------------------

criarTituloSecao("Lado que deve vencer");

const ladoAbrirInput = criarSelecao(
    "Qual lado deve abrir a válvula?",
    ["A", "B"]
);

// ------------------------------------------------------------
// Lado A
// ------------------------------------------------------------

criarTituloSecao("Lado A");

const pressaoAInput = criarCampoNumero(
    "Pressão atual em A",
    0,
    0,
    null,
    1,
    "bar"
);

const areaAInput = criarCampoNumero(
    "Área efetiva A",
    1,
    0.0001,
    null,
    0.1,
    "cm²"
);

// ------------------------------------------------------------
// Lado B
// ------------------------------------------------------------

criarTituloSecao("Lado B");

const pressaoBInput = criarCampoNumero(
    "Pressão atual em B",
    0,
    0,
    null,
    1,
    "bar"
);

const areaBInput = criarCampoNumero(
    "Área efetiva B",
    1,
    0.0001,
    null,
    0.1,
    "cm²"
);

// ------------------------------------------------------------
// Piloto X
// ------------------------------------------------------------

criarTituloSecao("Piloto X");

const pressaoXInput = criarCampoNumero(
    "Pressão piloto X",
    0,
    0,
    null,
    1,
    "bar"
);

const areaXInput = criarCampoNumero(
    "Área efetiva X",
    1,
    0,
    null,
    0.1,
    "cm²"
);

const ladoXInput = criarSelecao(
    "O piloto X ajuda qual lado?",
    ["A", "B"]
);

// ------------------------------------------------------------
// Mola
// ------------------------------------------------------------

criarTituloSecao("Mola");

const rigidezMolaInput = criarCampoNumero(
    "Rigidez da mola K",
    4000,
    0,
    null,
    100,
    "N/m"
);

const preCargaInput = criarCampoNumero(
    "Pré-compressão da mola",
    10,
    0,
    null,
    0.5,
    "mm"
);

const ladoMolaInput = criarSelecao(
    "A mola empurra para qual lado?",
    ["A", "B"]
);

// ------------------------------------------------------------
// Área de resultados
// ------------------------------------------------------------

criarTituloSecao("Resultados");

const resultados = document.createElement("div");
resultados.className = "resultados-calculadora";
container.appendChild(resultados);

// ------------------------------------------------------------
// Função auxiliar para formatar números
// ------------------------------------------------------------

function formatarNumero(valor, casas = 2) {
    return valor.toLocaleString("pt-BR", {
        minimumFractionDigits: casas,
        maximumFractionDigits: casas
    });
}

// ------------------------------------------------------------
// Função de cálculo
// ------------------------------------------------------------

function calcular() {
    const ladoAbrir = ladoAbrirInput.value;
    const ladoX = ladoXInput.value;
    const ladoMola = ladoMolaInput.value;

    const pA_bar = Number(pressaoAInput.value);
    const areaA_cm2 = Number(areaAInput.value);

    const pB_bar = Number(pressaoBInput.value);
    const areaB_cm2 = Number(areaBInput.value);

    const pX_bar = Number(pressaoXInput.value);
    const areaX_cm2 = Number(areaXInput.value);

    const K_N_m = Number(rigidezMolaInput.value);
    const preCarga_mm = Number(preCargaInput.value);

    // --------------------------------------------------------
    // Validação
    // --------------------------------------------------------

    const valoresInvalidos =
        pA_bar < 0 ||
        pB_bar < 0 ||
        pX_bar < 0 ||
        areaA_cm2 <= 0 ||
        areaB_cm2 <= 0 ||
        areaX_cm2 < 0 ||
        K_N_m < 0 ||
        preCarga_mm < 0;

    if (valoresInvalidos) {
        resultados.innerHTML = `
            <div class="aviso-erro">
                Verifique os valores informados. As áreas A e B
                precisam ser maiores que zero, e os demais valores
                não podem ser negativos.
            </div>
        `;
        return;
    }

    // --------------------------------------------------------
    // Conversões para o Sistema Internacional
    // --------------------------------------------------------

    const pA = pA_bar * 1e5;
    const pB = pB_bar * 1e5;
    const pX = pX_bar * 1e5;

    const areaA = areaA_cm2 * 1e-4;
    const areaB = areaB_cm2 * 1e-4;
    const areaX = areaX_cm2 * 1e-4;

    const x0 = preCarga_mm / 1000;

    // --------------------------------------------------------
    // Forças atuais
    // --------------------------------------------------------

    const FA_atual = pA * areaA;
    const FB_atual = pB * areaB;
    const FX = pX * areaX;
    const Fm = K_N_m * x0;

    // --------------------------------------------------------
    // Cálculo da força resistente
    // --------------------------------------------------------

    let F_resistente;
    let F_necessaria;
    let F_disponivel_atual;
    let areaAbertura;

    if (ladoAbrir === "A") {
        // B resiste ao movimento para A
        F_resistente = FB_atual;

        // X soma quando ajuda B e subtrai quando ajuda A
        if (ladoX === "B") {
            F_resistente += FX;
        } else {
            F_resistente -= FX;
        }

        // A mola soma quando empurra para B
        if (ladoMola === "B") {
            F_resistente += Fm;
        } else {
            F_resistente -= Fm;
        }

        F_disponivel_atual = FA_atual;
        areaAbertura = areaA;
    } else {
        // A resiste ao movimento para B
        F_resistente = FA_atual;

        // X soma quando ajuda A e subtrai quando ajuda B
        if (ladoX === "A") {
            F_resistente += FX;
        } else {
            F_resistente -= FX;
        }

        // A mola soma quando empurra para A
        if (ladoMola === "A") {
            F_resistente += Fm;
        } else {
            F_resistente -= Fm;
        }

        F_disponivel_atual = FB_atual;
        areaAbertura = areaB;
    }

    // A força mínima não pode ser negativa
    F_necessaria = Math.max(F_resistente, 0);

    // Pressão mínima no lado escolhido
    const p_necessaria_Pa = F_necessaria / areaAbertura;
    const p_necessaria_bar = p_necessaria_Pa / 1e5;

    // Margem positiva significa que o lado escolhido venceu
    const margem_forca =
        F_disponivel_atual - F_necessaria;

    // Igualdade representa equilíbrio, não abertura efetiva
    const abreAtualmente = margem_forca > 0;

    // --------------------------------------------------------
    // Estado visual
    // --------------------------------------------------------

    const classeEstado = abreAtualmente
        ? "estado-aberta"
        : "estado-fechada";

    const textoEstado = abreAtualmente
        ? "SIM — existe força para abrir"
        : margem_forca === 0
            ? "NÃO — forças em equilíbrio"
            : "NÃO — força insuficiente";

    const classeMargem =
        margem_forca < 0 ? "resultado-negativo" : "";

    // --------------------------------------------------------
    // Exibição dos resultados
    // --------------------------------------------------------

    resultados.innerHTML = `
        <div class="cartao-resultado">
            <span>Força hidráulica em A</span>
            <strong>${formatarNumero(FA_atual)} N</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força hidráulica em B</span>
            <strong>${formatarNumero(FB_atual)} N</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força do piloto X</span>
            <strong>${formatarNumero(FX)} N</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força da mola</span>
            <strong>${formatarNumero(Fm)} N</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força resistente resultante</span>
            <strong>${formatarNumero(F_resistente)} N</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força mínima necessária para abrir</span>
            <strong>${formatarNumero(F_necessaria)} N</strong>
        </div>

        <div class="cartao-resultado">
            <span>Pressão mínima necessária em ${ladoAbrir}</span>
            <strong>${formatarNumero(p_necessaria_bar)} bar</strong>
        </div>

        <div class="cartao-resultado ${classeMargem}">
            <span>Margem de força atual</span>
            <strong>${formatarNumero(margem_forca)} N</strong>
        </div>

        <div class="estado-valvula ${classeEstado}">
            <span>A válvula abre atualmente?</span>
            <strong>${textoEstado}</strong>
        </div>
    `;
}

// ------------------------------------------------------------
// Atualização automática
// ------------------------------------------------------------

const entradas = [
    ladoAbrirInput,
    pressaoAInput,
    areaAInput,
    pressaoBInput,
    areaBInput,
    pressaoXInput,
    areaXInput,
    ladoXInput,
    rigidezMolaInput,
    preCargaInput,
    ladoMolaInput
];

entradas.forEach(entrada => {
    entrada.addEventListener("input", calcular);
    entrada.addEventListener("change", calcular);
});

// Executa ao abrir a nota
calcular();

// ------------------------------------------------------------
// Fórmulas
// ------------------------------------------------------------

const formulas = document.createElement("div");
formulas.className = "formulas-calculadora";

formulas.innerHTML = `
    <h3>Fórmulas utilizadas</h3>

    <div class="formula">
        F = p · A
    </div>

    <div class="formula">
        F<sub>mola</sub> = K · x₀
    </div>

    <div class="formula">
        p<sub>necessária</sub> =
        F<sub>necessária</sub> /
        A<sub>abertura</sub>
    </div>

    <div class="formula">
        Margem =
        F<sub>disponível</sub> −
        F<sub>necessária</sub>
    </div>

    <div class="informacao">
        Margem positiva significa que o lado escolhido produz
        força maior que a força resistente. Margem igual a zero
        representa equilíbrio teórico; para ocorrer movimento real,
        normalmente é necessária uma diferença adicional para vencer
        atrito, vedação e forças dinâmicas.
    </div>
`;

container.appendChild(formulas);
```