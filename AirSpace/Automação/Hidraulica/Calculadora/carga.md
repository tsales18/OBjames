# Calculadora de Carga Hidráulica

```dataviewjs
// ============================================================
// CALCULADORA DE CARGA HIDRÁULICA
//
// F [N] = 10 × p [bar] × A [cm²]
// Utilização [%] = Fatual / Fmáxima × 100
// ============================================================

const container = dv.el("div", "");
container.className = "calculadora-orificio";

function criarTitulo(texto, nivel = 2) {
    const titulo = document.createElement(`h${nivel}`);
    titulo.textContent = texto;
    container.appendChild(titulo);
}

function criarCampoNumero(
    rotulo,
    valor,
    minimo,
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
    input.step = passo;

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

criarTitulo("Calculadora de Carga Hidráulica");
criarTitulo("Dados", 3);

const pressaoInput = criarCampoNumero(
    "Pressão atual",
    250,
    0,
    1,
    "bar"
);

const areaInput = criarCampoNumero(
    "Área efetiva",
    6310,
    0.01,
    1,
    "cm²"
);

const forcaMaximaInput = criarCampoNumero(
    "Força máxima do sistema",
    17651970,
    1,
    1000,
    "N"
);

// ------------------------------------------------------------
// Resultados
// ------------------------------------------------------------

criarTitulo("Força atual", 3);

const resultadosAtuais = document.createElement("div");
resultadosAtuais.className = "resultados-calculadora";
container.appendChild(resultadosAtuais);

criarTitulo("Capacidade do sistema", 3);

const capacidade = document.createElement("div");
capacidade.className = "resultados-calculadora";
container.appendChild(capacidade);

// ------------------------------------------------------------
// Cálculo
// ------------------------------------------------------------

function calcular() {
    const pressaoBar = Number(pressaoInput.value);
    const areaCm2 = Number(areaInput.value);
    const forcaMaximaN = Number(forcaMaximaInput.value);

    if (
        !Number.isFinite(pressaoBar) ||
        !Number.isFinite(areaCm2) ||
        !Number.isFinite(forcaMaximaN) ||
        pressaoBar < 0 ||
        areaCm2 <= 0 ||
        forcaMaximaN <= 0
    ) {
        resultadosAtuais.innerHTML = `
            <div class="aviso-erro">
                A pressão não pode ser negativa. A área e a força
                máxima devem ser maiores que zero.
            </div>
        `;

        capacidade.innerHTML = "";
        return;
    }

    // Força atual
    const forcaN = 10 * pressaoBar * areaCm2;
    const forcaKN = forcaN / 1000;
    const forcaMN = forcaN / 1_000_000;
    const forcaTf = forcaN / 9806.65;

    // Força máxima
    const forcaMaximaKN = forcaMaximaN / 1000;
    const forcaMaximaMN = forcaMaximaN / 1_000_000;
    const forcaMaximaTf = forcaMaximaN / 9806.65;

    // Percentual e margem
    const percentualUtilizado =
        (forcaN / forcaMaximaN) * 100;

    const margemDisponivelN =
        forcaMaximaN - forcaN;

    const margemDisponivelTf =
        margemDisponivelN / 9806.65;

    // Pressão correspondente à força máxima
    const pressaoMaximaBar =
        forcaMaximaN / (10 * areaCm2);

    const excedeuCapacidade =
        percentualUtilizado > 100;

    // A largura visual fica limitada a 100%
    const larguraBarra = Math.min(
        Math.max(percentualUtilizado, 0),
        100
    );

    let classeBarra = "barra-normal";

    if (percentualUtilizado >= 90) {
        classeBarra = "barra-atencao";
    }

    if (excedeuCapacidade) {
        classeBarra = "barra-excedida";
    }

    resultadosAtuais.innerHTML = `
        <div class="cartao-resultado">
            <span>Força atual</span>
            <strong>${formatarNumero(forcaN, 0)} N</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força atual</span>
            <strong>${formatarNumero(forcaKN, 2)} kN</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força atual</span>
            <strong>${formatarNumero(forcaMN, 3)} MN</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força atual</span>
            <strong>${formatarNumero(forcaTf, 2)} tf</strong>
        </div>
    `;

    capacidade.innerHTML = `
        <div class="cartao-resultado">
            <span>Força máxima</span>
            <strong>${formatarNumero(forcaMaximaN, 0)} N</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força máxima</span>
            <strong>${formatarNumero(forcaMaximaKN, 2)} kN</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força máxima</span>
            <strong>${formatarNumero(forcaMaximaMN, 3)} MN</strong>
        </div>

        <div class="cartao-resultado">
            <span>Força máxima</span>
            <strong>${formatarNumero(forcaMaximaTf, 2)} tf</strong>
        </div>

        <div class="cartao-resultado">
            <span>Pressão para atingir a força máxima</span>
            <strong>${formatarNumero(pressaoMaximaBar, 2)} bar</strong>
        </div>

        <div class="cartao-resultado ${
            excedeuCapacidade ? "resultado-negativo" : ""
        }">
            <span>Capacidade utilizada</span>
            <strong>${formatarNumero(percentualUtilizado, 2)} %</strong>
        </div>

        <div class="barra-capacidade-container">
            <div class="cabecalho-barra">
                <span>Utilização do sistema</span>
                <strong>${formatarNumero(percentualUtilizado, 2)}%</strong>
            </div>

            <div class="trilho-capacidade">
                <div
                    class="preenchimento-capacidade ${classeBarra}"
                    style="width: ${larguraBarra}%"
                ></div>
            </div>

            <div class="escala-capacidade">
                <span>0%</span>
                <span>50%</span>
                <span>100%</span>
            </div>
        </div>

        <div class="cartao-resultado ${
            margemDisponivelN < 0 ? "resultado-negativo" : ""
        }">
            <span>
                ${
                    margemDisponivelN >= 0
                        ? "Capacidade restante"
                        : "Capacidade excedida"
                }
            </span>

            <strong>
                ${formatarNumero(Math.abs(margemDisponivelN), 0)} N
            </strong>
        </div>

        <div class="cartao-resultado ${
            margemDisponivelTf < 0 ? "resultado-negativo" : ""
        }">
            <span>
                ${
                    margemDisponivelTf >= 0
                        ? "Capacidade restante"
                        : "Capacidade excedida"
                }
            </span>

            <strong>
                ${formatarNumero(Math.abs(margemDisponivelTf), 2)} tf
            </strong>
        </div>
    `;

    if (excedeuCapacidade) {
        capacidade.innerHTML += `
            <div class="aviso-erro">
                A força calculada ultrapassa a força máxima configurada
                para o sistema. Verifique a pressão, a área efetiva e o
                limite estrutural informado.
            </div>
        `;
    }
}

// ------------------------------------------------------------
// Atualização automática
// ------------------------------------------------------------

const entradas = [
    pressaoInput,
    areaInput,
    forcaMaximaInput
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
        F [N] =
        10 · p [bar] · A [cm²]
    </div>

    <div class="formula">
        F [tf] =
        F [N] / 9.806,65
    </div>

    <div class="formula">
        Utilização [%] =
        100 · F<sub>atual</sub> /
        F<sub>máxima</sub>
    </div>

    <div class="formula">
        Margem =
        F<sub>máxima</sub> −
        F<sub>atual</sub>
    </div>

    <div class="formula">
        p<sub>máxima</sub> [bar] =
        F<sub>máxima</sub> [N] /
        (10 · A [cm²])
    </div>

    <div class="informacao">
        A força máxima deve representar o menor limite aplicável ao
        conjunto: cilindro, tirantes, estrutura, ferramentas e demais
        componentes carregados. O valor não deve ser interpretado
        automaticamente como uma pressão segura de operação.
    </div>
`;

container.appendChild(formulas);
```