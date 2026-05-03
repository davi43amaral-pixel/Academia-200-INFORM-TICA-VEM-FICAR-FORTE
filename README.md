# Academia-200-INFORM-TICA-VEM-FICAR-FORTE
Superar seu eu

<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MALHE AQUI - Academia</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            background: #f5f5f5;
        }
        header {
            background: #e60014;
            color: white;
            padding: 15px;
            text-align: center;
            font-size: 24px;
        }
        .container {
            padding: 20px;
            max-width: 900px;
            margin: auto;
        }
        .card {
            background: white;
            padding: 25px;
            margin-bottom: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
        }
        input, select {
            width: 100%;
            padding: 12px;
            margin: 10px 0;
            border: 1px solid #ddd;
            border-radius: 6px;
            box-sizing: border-box;
            font-size: 16px;
        }
        button {
            padding: 14px;
            width: 100%;
            background: #e60014;
            color: white;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            margin-top: 10px;
            font-size: 16px;
            font-weight: bold;
        }
        button:hover {
            background: #c40010;
        }
        .agendamento {
            padding: 15px;
            margin-bottom: 12px;
            border: 1px solid #28a745;
            border-radius: 8px;
            background: #f8fff8;
        }
        .btn-cancelar {
            background: #dc3545;
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 8px;
        }
        .vazio {
            color: #666;
            text-align: center;
            padding: 30px;
            font-style: italic;
        }
        .admin-item {
            padding: 12px;
            background: #f9f9f9;
            margin-bottom: 8px;
            border-radius: 6px;
            border-left: 5px solid #e60014;
        }
    </style>
</head>
<body>

<header>MALHE AQUI - Academia</header>
<div class="container" id="app"></div>

<script>
// ==================== BANCO DE DADOS ====================
let db = JSON.parse(localStorage.getItem("dbMalheAqui")) || {
    usuarios: {},           // email: { senha: "...", nome: "..." }
    agendamentos: []        // array de agendamentos
};

// Função para salvar no localStorage
function salvarDB() {
    localStorage.setItem("dbMalheAqui", JSON.stringify(db));
}

let usuarioLogado = null;   // email do usuário logado

// ==================== TELAS ====================
function telaLogin() {
    document.getElementById("app").innerHTML = `
    <div class="card">
        <h2>Login</h2>
        <input id="email" type="email" placeholder="Seu e-mail" required>
        <input id="senha" type="password" placeholder="Senha" required>
        <button onclick="fazerLogin()">Entrar</button>
        <button onclick="telaCadastro()" style="background:#6c757d;">Criar conta</button>
    </div>`;
}

function telaCadastro() {
    document.getElementById("app").innerHTML = `
    <div class="card">
        <h2>Cadastro</h2>
        <input id="novoNome" type="text" placeholder="Seu nome completo">
        <input id="novoEmail" type="email" placeholder="Seu e-mail">
        <input id="novaSenha" type="password" placeholder="Senha (mín. 5 caracteres)">
        <button onclick="cadastrarUsuario()">Criar Conta</button>
        <button onclick="telaLogin()" style="background:#6c757d;">Voltar ao Login</button>
    </div>`;
}

// ==================== CADASTRO ====================
function cadastrarUsuario() {
    const nome = document.getElementById("novoNome").value.trim();
    const email = document.getElementById("novoEmail").value.trim().toLowerCase();
    const senha = document.getElementById("novaSenha").value.trim();

    if (!nome || !email || !senha) {
        return alert("Preencha todos os campos!");
    }
    if (senha.length < 5) {
        return alert("A senha deve ter pelo menos 5 caracteres!");
    }
    if (db.usuarios[email]) {
        return alert("Este e-mail já está cadastrado!");
    }

    db.usuarios[email] = {
        nome: nome,
        senha: senha
    };

    salvarDB();
    alert("✅ Conta criada com sucesso! Faça login.");
    telaLogin();
}

// ==================== LOGIN ====================
function fazerLogin() {
    const email = document.getElementById("email").value.trim().toLowerCase();
    const senha = document.getElementById("senha").value.trim();

    // Login do Admin (pode mudar depois)
    if (email === "academia1234@gmail.com" && senha === "12345") {
        usuarioLogado = "admin";
        telaAdmin();
        return;
    }

    if (db.usuarios[email] && db.usuarios[email].senha === senha) {
        usuarioLogado = email;
        telaUser();
    } else {
        alert("E-mail ou senha incorretos!");
    }
}

// ==================== TELA DO USUÁRIO ====================
function telaUser() {
    const usuario = db.usuarios[usuarioLogado];
    const nome = usuario ? usuario.nome : "Usuário";

    document.getElementById("app").innerHTML = `
    <div class="card">
        <h2>Olá, ${nome}!</h2>
        <p>Bem-vindo à Malhe Aqui</p>
    </div>

    <div class="card">
        <h2>Agendar Nutricionista</h2>
        <input type="date" id="dataNutri">
        <select id="horaNutri">
            <option value="">-- Selecione o horário --</option>
            <option value="08:00">08:00</option>
            <option value="10:00">10:00</option>
            <option value="14:00">14:00</option>
            <option value="16:00">16:00</option>
        </select>
        <button onclick="agendar('Nutri')">Agendar Nutricionista</button>
    </div>

    <div class="card">
        <h2>Agendar Treino</h2>
        <input type="date" id="dataTreino">
        <select id="horaTreino">
            <option value="">-- Selecione o horário --</option>
            <option value="06:00">06:00</option>
            <option value="08:00">08:00</option>
            <option value="18:00">18:00</option>
            <option value="20:00">20:00</option>
        </select>
        <button onclick="agendar('Treino')">Agendar Treino</button>
    </div>

    <div class="card">
        <h2>Meus Agendamentos</h2>
        <div id="listaAgendamentos"></div>
    </div>

    <button onclick="logout()">Sair</button>`;

    listarAgendamentos();
}

// ==================== AGENDAR ====================
function agendar(tipo) {
    const dataId = tipo === "Nutri" ? "dataNutri" : "dataTreino";
    const horaId = tipo === "Nutri" ? "horaNutri" : "horaTreino";

    const data = document.getElementById(dataId).value;
    const hora = document.getElementById(horaId).value;

    if (!data || !hora) {
        return alert("Por favor, escolha a data e o horário!");
    }

    // Validação de data no passado
    const dataEscolhida = new Date(data);
    const hoje = new Date();
    hoje.setHours(0, 0, 0, 0);

    if (dataEscolhida < hoje) {
        return alert("Não é possível agendar em datas passadas!");
    }

    // Verificar se já existe agendamento no mesmo dia e horário para este usuário
    const jaExiste = db.agendamentos.some(a => 
        a.user === usuarioLogado && 
        a.data === data && 
        a.hora === hora
    );

    if (jaExiste) {
        return alert("Você já possui um agendamento nesse dia e horário!");
    }

    // Criar novo agendamento
    db.agendamentos.push({
        id: Date.now(),
        user: usuarioLogado,
        tipo: tipo,
        data: data,
        hora: hora,
        criadoEm: new Date().toISOString()
    });

    salvarDB();
    alert(`✅ ${tipo} agendado com sucesso!`);
    listarAgendamentos();
}

// ==================== LISTAR AGENDAMENTOS DO USUÁRIO ====================
function listarAgendamentos() {
    const container = document.getElementById("listaAgendamentos");
    if (!container) return;

    const meusAgendamentos = db.agendamentos
        .filter(a => a.user === usuarioLogado)
        .sort((a, b) => {
            if (a.data !== b.data) return a.data.localeCompare(b.data);
            return a.hora.localeCompare(b.hora);
        });

    if (meusAgendamentos.length === 0) {
        container.innerHTML = `<p class="vazio">Você ainda não tem agendamentos.</p>`;
        return;
    }

    let html = "";
    meusAgendamentos.forEach(ag => {
        html += `
        <div class="agendamento">
            <strong>${ag.tipo}</strong><br>
            📅 ${ag.data} às ${ag.hora}<br>
            <button class="btn-cancelar" onclick="cancelarAgendamento(${ag.id})">Cancelar Agendamento</button>
        </div>`;
    });

    container.innerHTML = html;
}

// ==================== CANCELAR AGENDAMENTO ====================
function cancelarAgendamento(id) {
    if (!confirm("Deseja realmente cancelar este agendamento?")) return;

    const index = db.agendamentos.findIndex(a => a.id === id);
    if (index === -1) return;

    if (db.agendamentos[index].user === usuarioLogado) {
        db.agendamentos.splice(index, 1);
        salvarDB();
        listarAgendamentos();
        alert("Agendamento cancelado com sucesso.");
    }
}

// ==================== PAINEL ADMIN ====================
function telaAdmin() {
    document.getElementById("app").innerHTML = `
    <div class="card">
        <h2>👨‍💼 Painel do Administrador</h2>
        <p>Todos os agendamentos da academia</p>
        <div id="listaAdmin"></div>
    </div>
    <button onclick="logout()">Sair</button>`;

    listarAdmin();
}

function listarAdmin() {
    const container = document.getElementById("listaAdmin");
    if (!container) return;

    if (db.agendamentos.length === 0) {
        container.innerHTML = "<p class='vazio'>Nenhum agendamento registrado ainda.</p>";
        return;
    }

    let html = "";
    // Ordenar por data e hora
    const agendamentosOrdenados = [...db.agendamentos].sort((a, b) => {
        if (a.data !== b.data) return a.data.localeCompare(b.data);
        return a.hora.localeCompare(b.hora);
    });

    agendamentosOrdenados.forEach(a => {
        const usuario = db.usuarios[a.user] ? db.usuarios[a.user].nome : a.user;
        html += `
        <div class="admin-item">
            <strong>${usuario}</strong><br>
            ${a.tipo} — ${a.data} às ${a.hora}
        </div>`;
    });

    container.innerHTML = html;
}

// ==================== LOGOUT ====================
function logout() {
    usuarioLogado = null;
    telaLogin();
}

// ==================== INICIAR APLICAÇÃO ====================
telaLogin();

</script>
</body>
</html>
