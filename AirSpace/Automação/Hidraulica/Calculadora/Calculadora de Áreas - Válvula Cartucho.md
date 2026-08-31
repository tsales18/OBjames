# Calculadora de Áreas — Válvula Cartucho

```dataviewjs
// ============================================================
// CALCULADORA DE ÁREAS — VÁLVULA CARTUCHO
//
// Aap = Aa + Ab
// Ab  = k × Aa
// Aa  = Aap / (1 + k)
// ============================================================

const container = dv.el("div", "");
container.className = "calculadora-orificio";

// ------------------------------------------------------------
// Dados das válvulas
// Área total Aap em cm²
// ------------------------------------------------------------

const areasTotais = {
    16: 2.84,
    25: 6.16,
    32: 10.18,
    40: 16.62,
    50: 28.27,
    63: 44.20,
    80: 56.74,
    100: 95.00,
    125: 143.00,
    160: 240.50
};

// Relação Ab/Aa para cada tipo de spool
const tiposSpool = {
    "A — 50%": 0.50,
    "B — 7%": 0.07,
    "E — 107%": 1.07
};

// ------------------------------------------------------------
// Funções para construir a interface
// ------------------------------------------------------------

function criarTitulo(texto, nivel = 2) {
    const titulo = document.createElement(`h${nivel}`);
    titulo.textContent = texto;
    container.appendChild(titulo);
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
        item.value = opcao.valor;
        item.textContent = opcao.texto;
        select.appendChild(item);
    }

    linha.appendChild(select);
    grupo.appendChild(label);
    grupo.appendChild(linha);
    container.appendChild(grupo);

    return select;
}

function formatarNumero(valor, casas = 4) {
    return valor.toLocaleString("pt-BR", {
        minimumFractionDigits: casas,
        maximumFractionDigits: casas
    });
}

// ------------------------------------------------------------
// Título e entradas
// ------------------------------------------------------------

criarTitulo("Calculadora de Áreas — Válvula Cartucho");

criarTitulo("Dados da válvula", 3);

const opcoesTamanho = Object.keys(areasTotais).map(tamanho => ({
    valor: tamanho,
    texto: `Tamanho ${tamanho}`
}));

const opcoesSpool = Object.keys(tiposSpool).map(tipo => ({
    valor: tipo,
    texto: tipo
}));

const tamanhoInput = criarSelecao(
    "Tamanho da válvula",
    opcoesTamanho
);

const tipoInput = criarSelecao(
    "Tipo do spool",
    opcoesSpool
);

// ------------------------------------------------------------
// Resultados
// ------------------------------------------------------------

criarTitulo("Resultados", 3);

const resultados = document.createElement("div");
resultados.className = "resultados-calculadora";
container.appendChild(resultados);

// ------------------------------------------------------------
// Representação visual das áreas
// ------------------------------------------------------------

const representacao = document.createElement("div");
representacao.className = "representacao-areas";
container.appendChild(representacao);

// ------------------------------------------------------------
// Cálculo
// ------------------------------------------------------------

function calcular() {
    const tamanho = Number(tamanhoInput.value);
    const tipo = tipoInput.value;

    const Aap = areasTotais[tamanho];
    const k = tiposSpool[tipo];

    /*
        Aap = Aa + Ab
        Ab = k × Aa

        Substituindo Ab:

        Aap = Aa + k × Aa
        Aap = Aa × (1 + k)

        Portanto:

        Aa = Aap / (1 + k)
    */

    const Aa = Aap / (1 + k);
    const Ab = Aap - Aa;

    const relacaoAapAa = Aap / Aa;
    const relacaoAbAa = Ab / Aa;

    const percentualAa = (Aa / Aap) * 100;
    const percentualAb = (Ab / Aap) * 100;

    resultados.innerHTML = `
        <div class="cartao-resultado">
            <span>Área total Aap</span>
            <strong>${formatarNumero(Aap)} cm²</strong>
        </div>

        <div class="cartao-resultado">
            <span>Área Aa</span>
            <strong>${formatarNumero(Aa)} cm²</strong>
        </div>

        <div class="cartao-resultado">
            <span>Área Ab</span>
            <strong>${formatarNumero(Ab)} cm²</strong>
        </div>

        <div class="cartao-resultado">
            <span>Relação Aap : Aa</span>
            <strong>${formatarNumero(relacaoAapAa)} : 1</strong>
        </div>

        <div class="cartao-resultado">
            <span>Relação Ab : Aa</span>
            <strong>${formatarNumero(relacaoAbAa)} : 1</strong>
        </div>

        <div class="cartao-resultado">
            <span>Tipo selecionado</span>
            <strong>${tipo}</strong>
        </div>
    `;

    representacao.innerHTML = `
        <h3>Distribuição da área total Aap</h3>

        <div class="barra-areas">
            <div
                class="barra-aa"
                style="width: ${percentualAa}%"
                title="Aa = ${formatarNumero(Aa)} cm²"
            >
                Aa
            </div>

            <div
                class="barra-ab"
                style="width: ${percentualAb}%"
                title="Ab = ${formatarNumero(Ab)} cm²"
            >
                Ab
            </div>
        </div>

        <div class="legenda-areas">
            <div>
                <span class="cor-aa"></span>
                Aa representa
                <strong>${formatarNumero(percentualAa, 2)}%</strong>
                de Aap
            </div>

            <div>
                <span class="cor-ab"></span>
                Ab representa
                <strong>${formatarNumero(percentualAb, 2)}%</strong>
                de Aap
            </div>
        </div>
    `;
}

// ------------------------------------------------------------
// Atualização automática
// ------------------------------------------------------------

tamanhoInput.addEventListener("change", calcular);
tipoInput.addEventListener("change", calcular);

calcular();

// ------------------------------------------------------------
// Fórmulas
// ------------------------------------------------------------

const formulas = document.createElement("div");
formulas.className = "formulas-calculadora";

formulas.innerHTML = `
    <h3>Relações utilizadas</h3>

    <div class="formula">
        A<sub>ap</sub> =
        A<sub>a</sub> + A<sub>b</sub>
    </div>

    <div class="formula">
        A<sub>b</sub> =
        k · A<sub>a</sub>
    </div>

    <div class="formula">
        A<sub>a</sub> =
        A<sub>ap</sub> / (1 + k)
    </div>

    <div class="formula">
        A<sub>b</sub> =
        A<sub>ap</sub> − A<sub>a</sub>
    </div>

    <div class="formula">
        A<sub>ap</sub> / A<sub>a</sub> =
        1 + k
    </div>

    <div class="informacao">
        O percentual indicado pelo tipo do spool representa a relação
        <strong>Ab/Aa</strong>, e não a porcentagem direta de Ab dentro
        de Aap. Por isso, em um spool A de 50%, Ab corresponde a
        aproximadamente 33,33% da área total Aap.
    </div>
`;

container.appendChild(formulas);
```
