
```dataviewjs
// ============================================================
// CALCULADORA DE QUEDA DE PRESSÃO EM ORIFÍCIO
// Equação:
// Δp = (ρ / 2) × [Q / (Cd × A)]²
// ============================================================

const container = dv.el("div", "");
container.className = "calculadora-orificio";

// ---------- Título ----------

const titulo = document.createElement("h2");
titulo.textContent = "Calculadora de Queda de Pressão em Orifício";
container.appendChild(titulo);

const subtitulo = document.createElement("h3");
subtitulo.textContent = "Dados de entrada";
container.appendChild(subtitulo);

// ---------- Função para criar campos ----------

function criarCampo(rotulo, valor, minimo, maximo, passo, unidade) {
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

// ---------- Entradas ----------

const diametroInput = criarCampo(
    "Diâmetro do orifício",
    5.0,
    0.01,
    null,
    0.1,
    "mm"
);

const vazaoInput = criarCampo(
    "Vazão através do orifício",
    10.0,
    0,
    null,
    0.1,
    "L/min"
);

const cdInput = criarCampo(
    "Coeficiente de descarga Cd",
    0.65,
    0.01,
    1.0,
    0.01,
    ""
);

const densidadeInput = criarCampo(
    "Densidade do óleo",
    850,
    1,
    null,
    10,
    "kg/m³"
);

const pressaoAInput = criarCampo(
    "Pressão em A",
    220,
    0,
    null,
    1,
    "bar"
);

// ---------- Área de resultados ----------

const tituloResultados = document.createElement("h3");
tituloResultados.textContent = "Resultados";
container.appendChild(tituloResultados);

const resultados = document.createElement("div");
resultados.className = "resultados-calculadora";
container.appendChild(resultados);

// ---------- Função de cálculo ----------

function calcular() {
    const diametro_mm = Number(diametroInput.value);
    const vazao_lmin = Number(vazaoInput.value);
    const cd = Number(cdInput.value);
    const densidade = Number(densidadeInput.value);
    const pressao_A_bar = Number(pressaoAInput.value);

    // Validação
    if (
        diametro_mm <= 0 ||
        vazao_lmin < 0 ||
        cd <= 0 ||
        cd > 1 ||
        densidade <= 0 ||
        pressao_A_bar < 0
    ) {
        resultados.innerHTML = `
            <div class="aviso-erro">
                Verifique os valores informados.
            </div>
        `;
        return;
    }

    // Conversões para o Sistema Internacional
    const diametro_m = diametro_mm / 1000;
    const vazao_m3s = vazao_lmin / 60000;

    // Área circular do orifício
    const area_m2 = Math.PI * Math.pow(diametro_m, 2) / 4;

    let delta_p_pa = 0;

    if (vazao_m3s > 0 && area_m2 > 0) {
        delta_p_pa =
            (densidade / 2) *
            Math.pow(
                vazao_m3s / (cd * area_m2),
                2
            );
    }

    const delta_p_bar = delta_p_pa / 100000;
    const pressao_X_bar = pressao_A_bar - delta_p_bar;
    const velocidade_ms =
        area_m2 > 0 ? vazao_m3s / area_m2 : 0;

    // A pressão negativa significa que a vazão solicitada não pode
    // ser obtida somente com a pressão disponível em A.
    const pressaoXClasse =
        pressao_X_bar < 0 ? "resultado-negativo" : "";

    resultados.innerHTML = `
        <div class="cartao-resultado">
            <span>Área do orifício</span>
            <strong>${(area_m2 * 1e6).toFixed(3)} mm²</strong>
        </div>

        <div class="cartao-resultado">
            <span>Velocidade no orifício</span>
            <strong>${velocidade_ms.toFixed(2)} m/s</strong>
        </div>

        <div class="cartao-resultado">
            <span>Queda de pressão Δp</span>
            <strong>${delta_p_bar.toFixed(2)} bar</strong>
        </div>

        <div class="cartao-resultado ${pressaoXClasse}">
            <span>Pressão estimada em X</span>
            <strong>${pressao_X_bar.toFixed(2)} bar</strong>
        </div>
    `;

    if (pressao_X_bar < 0) {
        resultados.innerHTML += `
            <div class="aviso-erro">
                A queda calculada é maior que a pressão disponível em A.
                Nessas condições, a vazão informada não pode atravessar
                esse orifício mantendo a pressão de saída acima de 0 bar.
            </div>
        `;
    }
}

// ---------- Atualização automática ----------

const entradas = [
    diametroInput,
    vazaoInput,
    cdInput,
    densidadeInput,
    pressaoAInput
];

entradas.forEach(input => {
    input.addEventListener("input", calcular);
});

// Executa ao abrir a nota
calcular();

// ---------- Fórmulas e observação ----------

const formulas = document.createElement("div");
formulas.className = "formulas-calculadora";

formulas.innerHTML = `
    <h3>Fórmulas utilizadas</h3>

    <div class="formula">
        A<sub>o</sub> = πd² / 4
    </div>

    <div class="formula">
        Q = C<sub>d</sub>A<sub>o</sub>
        √(2Δp / ρ)
    </div>

    <div class="formula">
        Δp = (ρ / 2)
        [Q / (C<sub>d</sub>A<sub>o</sub>)]²
    </div>

    <div class="formula">
        p<sub>X</sub> = p<sub>A</sub> − Δp
    </div>

    <div class="informacao">
        A relação é aproximadamente
        <strong>Δp ∝ Q² / d⁴</strong>.
        Pequenas reduções no diâmetro podem aumentar muito
        a queda de pressão.
    </div>
`;

container.appendChild(formulas);
```
