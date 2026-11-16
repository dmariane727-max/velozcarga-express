# velozcarga-express
Projeto prático de PHP - Gerenciador logístico VelozCarga Express


<?php
if (session_status() == PHP_SESSION_NONE) {
    session_start();
}

// Inicializa com os dados já prontos para você não ter trabalho
if (!isset($_SESSION['produtos'])) {$_SESSION['produtos'] = [
        ['id' => 'p1', 'nome' => 'Caixa de Eletrônicos', 'peso' => 12.50, 'valor' => 1500.00],
        ['id' => 'p2', 'nome' => 'Peças Automotivas', 'peso' => 35.00, 'valor' => 3200.00],
        ['id' => 'p3', 'nome' => 'Insumos Hospitalares', 'peso' => 8.20, 'valor' => 950.00]
    ];
}

if (!isset($_SESSION['enderecos'])) {$_SESSION['enderecos'] = [
        ['id' => 'e1', 'cep' => '86600-000', 'rua' => 'Av. Interventor Manoel Ribas, 1000', 'cidade' => 'Rolândia', 'tipo' => 'Origem'],
        ['id' => 'e2', 'cep' => '86010-000', 'rua' => 'Rua Sergipe, 500', 'cidade' => 'Londrina', 'tipo' => 'Destino']
    ];
}

if (!isset($_SESSION['transportadores'])) {$_SESSION['transportadores'] = [
        ['id' => 't1', 'nome' => 'Carlos Silva', 'veiculo' => 'Mercedes-Benz Sprinter (ABC-1234)'],
        ['id' => 't2', 'nome' => 'Ana Souza', 'veiculo' => 'Hyundai HR Baú (XYZ-9876)']
    ];
}

if (!isset($_SESSION['fretes']))$_SESSION['fretes'] = [];
?>

<?php include 'conexao.php'; ?>
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>VelozCarga Express - Home</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <div class="container">
            <a class="navbar-brand" href="index.php">🚚 VelozCarga Express</a>
            <div class="navbar-nav">
                <a class="nav-link" href="produto.php">Produtos</a>
                <a class="nav-link" href="endereco.php">Endereços</a>
                <a class="nav-link" href="transportador.php">Transportadores</a>
                <a class="nav-link" href="frete.php">Fretes</a>
                <a class="nav-link" href="relatorio.php">Relatórios</a>
            </div>
        </div>
    </nav>
    <div class="container mt-5 text-center">
        <h1>VelozCarga Express</h1>
        <p class="lead">Sistema de Gerenciamento Logístico carregado com os dados prontos!</p>
        <hr>
        <p>Clique no menu acima para conferir os cadastros e gerar o seu frete para o vídeo.</p>
    </div>
</body>
</html>

<?php 
include 'conexao.php';
if (isset($_POST['salvar'])) {
    $produto = ['id' => uniqid(), 'nome' => $_POST['nome'], 'peso' => $_POST['peso'], 'valor' => $_POST['valor']];
    $_SESSION['produtos'][] = $produto;
    header("Location: produto.php");
    exit;
}
?>
<!DOCTYPE html>
<html lang="pt-BR">
<head><meta charset="UTF-8"><title>Produtos</title>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet"></head>
<body class="container mt-4">
    <a href="index.php" class="btn btn-secondary mb-3">⬅ Voltar</a>
    <h2>Produtos Cadastrados</h2>
    <ul class="list-group mb-4">
        <?php foreach($_SESSION['produtos'] as $p): ?>
            <li class="list-group-item"><strong><?= $p['nome'] ?></strong> - Peso: <?= $p['peso'] ?>kg - R$ <?= number_format($p['valor'], 2, ',', '.') ?></li>
        <?php endforeach; ?>
    </ul>
    <hr>
    <h4>Adicionar Novo Produto (Opcional)</h4>
    <form method="POST" class="card p-3 bg-white shadow-sm">
        <div class="mb-3"><label>Nome:</label><input type="text" name="nome" class="form-control" required></div>
        <div class="mb-3"><label>Peso (kg):</label><input type="number" step="0.01" name="peso" class="form-control" required></div>
        <div class="mb-3"><label>Valor (R$):</label><input type="number" step="0.01" name="valor" class="form-control" required></div>
        <button type="submit" name="salvar" class="btn btn-primary">Cadastrar</button>
    </form>
</body>
</html>


<?php 
include 'conexao.php';
if (isset($_POST['salvar'])) {
    $end = ['id' => uniqid(), 'cep' => $_POST['cep'], 'rua' => $_POST['rua'], 'cidade' => $_POST['cidade'], 'tipo' => $_POST['tipo']];
    $_SESSION['enderecos'][] = $end;
    header("Location: endereco.php");
    exit;
}
?>
<!DOCTYPE html>
<html lang="pt-BR">
<head><meta charset="UTF-8"><title>Endereços</title>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet"></head>
<body class="container mt-4">
    <a href="index.php" class="btn btn-secondary mb-3">⬅ Voltar</a>
    <h2>Endereços Cadastrados</h2>
    <ul class="list-group mb-4">
        <?php foreach($_SESSION['enderecos'] as $e): ?>
            <li class="list-group-item">[<strong><?= $e['tipo'] ?></strong>] <?= $e['rua'] ?>, <?= $e['cidade'] ?> - CEP: <?= $e['cep'] ?></li>
        <?php endforeach; ?>
    </ul>
    <hr>
    <h4>Adicionar Novo Endereço</h4>
    <form method="POST" class="card p-3 bg-white shadow-sm">
        <div class="mb-3"><label>Tipo:</label><select name="tipo" class="form-control"><option value="Origem">Origem</option><option value="Destino">Destino</option></select></div>
        <div class="mb-3"><label>CEP:</label><input type="text" name="cep" class="form-control" required></div>
        <div class="mb-3"><label>Rua / Número:</label><input type="text" name="rua" class="form-control" required></div>
        <div class="mb-3"><label>Cidade:</label><input type="text" name="cidade" class="form-control" required></div>
        <button type="submit" name="salvar" class="btn btn-primary">Cadastrar</button>
    </form>
</body>
</html>


<?php 
include 'conexao.php';
if (isset($_POST['salvar'])) {
    $t = ['id' => uniqid(), 'nome' => $_POST['nome'], 'veiculo' => $_POST['veiculo']];
    $_SESSION['transportadores'][] = $t;
    header("Location: transportador.php");
    exit;
}
?>
<!DOCTYPE html>
<html lang="pt-BR">
<head><meta charset="UTF-8"><title>Transportadores</title>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet"></head>
<body class="container mt-4">
    <a href="index.php" class="btn btn-secondary mb-3">⬅ Voltar</a>
    <h2>Transportadores Cadastrados</h2>
    <ul class="list-group mb-4">
        <?php foreach($_SESSION['transportadores'] as $tr): ?>
            <li class="list-group-item"><strong><?= $tr['nome'] ?></strong> - Veículo: <?= $tr['veiculo'] ?></li>
        <?php endforeach; ?>
    </ul>
    <hr>
    <h4>Adicionar Novo Transportador</h4>
    <form method="POST" class="card p-3 bg-white shadow-sm">
        <div class="mb-3"><label>Nome:</label><input type="text" name="nome" class="form-control" required></div>
        <div class="mb-3"><label>Veículo / Placa:</label><input type="text" name="veiculo" class="form-control" required></div>
        <button type="submit" name="salvar" class="btn btn-primary">Cadastrar</button>
    </form>
</body>
</html>

<?php 
include 'conexao.php';
if (isset($_POST['salvar'])) {
    $f = [
        'id' => uniqid(),
        'transportador' => $_POST['transportador'],
        'produto' => $_POST['produto'],
        'data' => date('Y-m-d')
    ];
    $_SESSION['fretes'][] = $f;
    header("Location: frete.php");
    exit;
}
?>
<!DOCTYPE html>
<html lang="pt-BR">
<head><meta charset="UTF-8"><title>Fretes</title>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet"></head>
<body class="container mt-4">
    <a href="index.php" class="btn btn-secondary mb-3">⬅ Voltar</a>
    <h2>Gerenciar Fretes (1 para Muitos)</h2>
    <form method="POST" class="card p-3 mb-4 bg-white shadow-sm">
        <div class="mb-3"><label>Transportador:</label>
            <select name="transportador" class="form-control" required>
                <?php foreach($_SESSION['transportadores'] as $tr): ?>
                    <option value="<?= $tr['nome'] ?>"><?= $tr['nome'] ?> (<?= $tr['veiculo'] ?>)</option>
                <?php endforeach; ?>
            </select>
        </div>
        <div class="mb-3"><label>Produto:</label>
            <select name="produto" class="form-control" required>
                <?php foreach($_SESSION['produtos'] as $p): ?>
                    <option value="<?= $p['nome'] ?>"><?= $p['nome'] ?> (R$ <?= number_format($p['valor'], 2, ',', '.') ?>)</option>
                <?php endforeach; ?>
            </select>
        </div>
        <button type="submit" name="salvar" class="btn btn-success">Gerar Frete</button>
    </form>
    <h3>Fretes Gerados</h3>
    <ul class="list-group">
        <?php foreach($_SESSION['fretes'] as $fr): ?>
            <li class="list-group-item">Transportador: <strong><?= $fr['transportador'] ?></strong> | Produto: <strong><?= $fr['produto'] ?></strong> (Data: <?= $fr['data'] ?>)</li>
        <?php endforeach; ?>
    </ul>
</body>
</html>

<?php include 'conexao.php'; ?>
<!DOCTYPE html>
<html lang="pt-BR">
<head><meta charset="UTF-8"><title>Relatório</title>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet"></head>
<body class="container mt-4">
    <a href="index.php" class="btn btn-secondary mb-3">⬅ Voltar</a>
    <h2>Relatório Diário de Fretes</h2>
    <button onclick="window.print()" class="btn btn-dark mb-3">🖨️ Baixar / Imprimir Relatório (PDF)</button>
    <table class="table table-bordered">
        <thead><tr><th>ID Frete</th><th>Transportador</th><th>Produto</th><th>Data</th></tr></thead>
        <tbody>
            <?php foreach($_SESSION['fretes'] as $fr): ?>
                <tr>
                    <td><?= $fr['id'] ?></td>
                    <td><?= $fr['transportador'] ?></td>
                    <td><?= $fr['produto'] ?></td>
                    <td><?= $fr['data'] ?></td>
                </tr>
            <?php endforeach; ?>
        </tbody>
    </table>
</body>
</html>

