<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Finanças Jade 💰</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        /* Esconde as abas que não estão ativas */
        .tab-content { display: none; }
        .tab-content.active { display: block; }
    </style>
</head>
<body class="bg-gray-100 font-sans leading-normal tracking-normal h-screen flex flex-col">

    <nav class="bg-emerald-700 text-white shadow-lg">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-16">
                <div class="flex items-center">
                    <span class="text-2xl font-bold"><i class="fas fa-wallet mr-2"></i> JADE</span>
                </div>
                <div class="flex space-x-4">
                    <button onclick="mudarAba('resumo')" id="btn-resumo" class="bg-emerald-900 px-3 py-2 rounded-md text-sm font-medium hover:bg-emerald-800 transition">Resumo Geral</button>
                    <button onclick="mudarAba('entradas')" id="btn-entradas" class="px-3 py-2 rounded-md text-sm font-medium hover:bg-emerald-800 transition">Entradas</button>
                    <button onclick="mudarAba('saidas')" id="btn-saidas" class="px-3 py-2 rounded-md text-sm font-medium hover:bg-emerald-800 transition">Saídas</button>
                </div>
            </div>
        </div>
    </nav>

    <div class="flex-grow max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8 w-full">

        <div id="aba-resumo" class="tab-content active space-y-6">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-2xl font-bold text-gray-800">Visão Geral</h2>
                
                <div class="relative">
                    <input type="file" id="csvFileInput" accept=".csv" class="hidden" onchange="importarNubank(event)">
                    <button onclick="document.getElementById('csvFileInput').click()" class="bg-purple-600 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded shadow flex items-center">
                        <i class="fas fa-file-csv mr-2"></i> Importar Nubank (CSV)
                    </button>
                </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
                <div class="bg-white p-6 rounded-lg shadow border-l-4 border-blue-500">
                    <h3 class="text-gray-500 text-sm font-bold uppercase">Saldo Geral</h3>
                    <p class="text-3xl font-bold text-blue-600 mt-2" id="resumo-saldo">R$ 0,00</p>
                </div>
                <div class="bg-white p-6 rounded-lg shadow border-l-4 border-emerald-500">
                    <h3 class="text-gray-500 text-sm font-bold uppercase">Total Entradas</h3>
                    <p class="text-xl font-bold text-emerald-600 mt-2" id="resumo-entradas">R$ 0,00</p>
                </div>
                <div class="bg-white p-6 rounded-lg shadow border-l-4 border-red-500">
                    <h3 class="text-gray-500 text-sm font-bold uppercase">Total Saídas</h3>
                    <p class="text-xl font-bold text-red-600 mt-2" id="resumo-saidas">R$ 0,00</p>
                </div>
            </div>

            <div class="bg-white p-6 rounded-lg shadow">
                <h2 class="text-xl font-bold mb-4 border-b pb-2">Gastos por Categoria</h2>
                <div id="container-categorias" class="grid grid-cols-2 md:grid-cols-4 gap-4">
                    </div>
            </div>
        </div>

        <div id="aba-entradas" class="tab-content space-y-6">
            <h2 class="text-2xl font-bold text-gray-800 mb-6">Receitas e Entradas</h2>
            
            <div class="bg-white p-6 rounded-lg shadow mb-6 border-t-4 border-emerald-500">
                <form onsubmit="adicionarTransacao(event, 'entrada')" class="grid grid-cols-1 md:grid-cols-4 gap-4">
                    <input type="text" id="ent-nome" placeholder="Descrição (Ex: Salário)" required class="border p-2 rounded focus:ring-emerald-500">
                    <input type="number" id="ent-valor" step="0.01" placeholder="Valor (R$)" required class="border p-2 rounded focus:ring-emerald-500">
                    <input type="date" id="ent-data" required class="border p-2 rounded focus:ring-emerald-500">
                    <button type="submit" class="bg-emerald-600 text-white font-bold py-2 px-4 rounded hover:bg-emerald-700">Adicionar Entrada</button>
                </form>
            </div>

            <div class="bg-white rounded-lg shadow overflow-x-auto">
                <table class="min-w-full divide-y divide-gray-200">
                    <thead class="bg-emerald-50">
                        <tr>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Data</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Descrição</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Valor</th>
                            <th class="px-6 py-3 text-center text-xs font-medium text-gray-500 uppercase">Ação</th>
                        </tr>
                    </thead>
                    <tbody id="tabela-entradas" class="bg-white divide-y divide-gray-200"></tbody>
                </table>
            </div>
        </div>

        <div id="aba-saidas" class="tab-content space-y-6">
            <h2 class="text-2xl font-bold text-gray-800 mb-6">Despesas e Saídas</h2>

            <div class="bg-white p-6 rounded-lg shadow mb-6 border-t-4 border-red-500">
                <form onsubmit="adicionarTransacao(event, 'saida')" class="grid grid-cols-1 md:grid-cols-5 gap-4">
                    <input type="text" id="sai-nome" placeholder="Descrição (Ex: Conta de Luz)" required class="border p-2 rounded focus:ring-red-500">
                    <select id="sai-categoria" class="border p-2 rounded focus:ring-red-500">
                        <option value="Moradia">Moradia</option>
                        <option value="Mercado">Mercado</option>
                        <option value="Contas">Contas Fixas</option>
                        <option value="Transporte">Transporte</option>
                        <option value="Saúde">Saúde</option>
                        <option value="Cartão de Crédito">Cartão de Crédito</option>
                        <option value="💳 Importado Nubank">💳 Importado Nubank</option>
                        <option value="Diversos">Diversos</option>
                    </select>
                    <input type="number" id="sai-valor" step="0.01" placeholder="Valor (R$)" required class="border p-2 rounded focus:ring-red-500">
                    <input type="date" id="sai-data" required class="border p-2 rounded focus:ring-red-500">
                    <button type="submit" class="bg-red-600 text-white font-bold py-2 px-4 rounded hover:bg-red-700">Adicionar Saída</button>
                </form>
            </div>

            <div class="bg-white rounded-lg shadow overflow-x-auto">
                <table class="min-w-full divide-y divide-gray-200">
                    <thead class="bg-red-50">
                        <tr>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Data</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Descrição</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Categoria</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Valor</th>
                            <th class="px-6 py-3 text-center text-xs font-medium text-gray-500 uppercase">Ação</th>
                        </tr>
                    </thead>
                    <tbody id="tabela-saidas" class="bg-white divide-y divide-gray-200"></tbody>
                </table>
            </div>
        </div>

    </div>

    <script>
        // Banco de dados em memória
        let transacoes = [];

        // Navegação entre abas
        function mudarAba(abaId) {
            // Esconde todas as abas
            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            // Remove destaque dos botões
            document.querySelectorAll('nav button').forEach(el => el.classList.remove('bg-emerald-900'));
            
            // Ativa a aba clicada
            document.getElementById(`aba-${abaId}`).classList.add('active');
            document.getElementById(`btn-${abaId}`).classList.add('bg-emerald-900');
            
            atualizarInterface();
        }

        // Adicionar manualmente
        function adicionarTransacao(e, tipo) {
            e.preventDefault();
            
            let prefixo = tipo === 'entrada' ? 'ent' : 'sai';
            let novaTransacao = {
                id: Date.now(),
                tipo: tipo,
                nome: document.getElementById(`${prefixo}-nome`).value,
                valor: parseFloat(document.getElementById(`${prefixo}-valor`).value),
                data: document.getElementById(`${prefixo}-data`).value,
                categoria: tipo === 'saida' ? document.getElementById(`${prefixo}-categoria`).value : 'Renda'
            };

            transacoes.push(novaTransacao);
            e.target.reset(); // Limpa o formulário
            atualizarInterface();
            alert(`${tipo.toUpperCase()} adicionada com sucesso!`);
        }

        function excluirTransacao(id) {
            transacoes = transacoes.filter(t => t.id !== id);
            atualizarInterface();
        }

        // Formatação
        function formatarMoeda(valor) {
            return Math.abs(valor).toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' });
        }
        function formatarData(dataIso) {
            if (!dataIso) return '-';
            const partes = dataIso.split('-');
            return `${partes[2]}/${partes[1]}/${partes[0]}`;
        }

        // Lógica Principal de Atualização
        function atualizarInterface() {
            let totalEntradas = 0;
            let totalSaidas = 0;
            let gastosPorCategoria = {};

            const tbEntradas = document.getElementById('tabela-entradas');
            const tbSaidas = document.getElementById('tabela-saidas');
            
            tbEntradas.innerHTML = '';
            tbSaidas.innerHTML = '';

            // Ordena por data mais recente
            transacoes.sort((a, b) => new Date(b.data) - new Date(a.data));

            transacoes.forEach(t => {
                if (t.tipo === 'entrada') {
                    totalEntradas += t.valor;
                    tbEntradas.innerHTML += `
                        <tr>
                            <td class="px-6 py-4 text-sm text-gray-500">${formatarData(t.data)}</td>
                            <td class="px-6 py-4 text-sm font-medium text-gray-900">${t.nome}</td>
                            <td class="px-6 py-4 text-sm font-bold text-emerald-600">${formatarMoeda(t.valor)}</td>
                            <td class="px-6 py-4 text-center text-sm">
                                <button onclick="excluirTransacao(${t.id})" class="text-red-500 hover:text-red-700"><i class="fas fa-trash"></i></button>
                            </td>
                        </tr>
                    `;
                } else {
                    totalSaidas += t.valor;
                    gastosPorCategoria[t.categoria] = (gastosPorCategoria[t.categoria] || 0) + t.valor;
                    
                    tbSaidas.innerHTML += `
                        <tr>
                            <td class="px-6 py-4 text-sm text-gray-500">${formatarData(t.data)}</td>
                            <td class="px-6 py-4 text-sm font-medium text-gray-900">${t.nome}</td>
                            <td class="px-6 py-4 text-sm"><span class="bg-gray-100 text-gray-600 px-2 py-1 rounded text-xs">${t.categoria}</span></td>
                            <td class="px-6 py-4 text-sm font-bold text-red-600">${formatarMoeda(t.valor)}</td>
                            <td class="px-6 py-4 text-center text-sm">
                                <button onclick="excluirTransacao(${t.id})" class="text-red-500 hover:text-red-700"><i class="fas fa-trash"></i></button>
                            </td>
                        </tr>
                    `;
                }
            });

            // Atualiza Dashboard
            const saldo = totalEntradas - totalSaidas;
            document.getElementById('resumo-entradas').innerText = formatarMoeda(totalEntradas);
            document.getElementById('resumo-saidas').innerText = formatarMoeda(totalSaidas);
            document.getElementById('resumo-saldo').innerText = formatarMoeda(saldo);
            document.getElementById('resumo-saldo').className = saldo < 0 ? "text-3xl font-bold text-red-600 mt-2" : "text-3xl font-bold text-blue-600 mt-2";

            // Atualiza Categorias
            const divCategorias = document.getElementById('container-categorias');
            divCategorias.innerHTML = '';
            
            Object.entries(gastosPorCategoria)
                .sort((a, b) => b[1] - a[1]) // Ordena do maior pro menor gasto
                .forEach(([cat, valor]) => {
                    let porcentagem = totalSaidas > 0 ? ((valor / totalSaidas) * 100).toFixed(1) : 0;
                    divCategorias.innerHTML += `
                        <div class="border rounded p-4 bg-gray-50 shadow-sm flex flex-col justify-between">
                            <span class="text-sm font-bold text-gray-700 mb-1">${cat}</span>
                            <span class="text-lg font-bold text-red-500">${formatarMoeda(valor)}</span>
                            <span class="text-xs text-gray-400 mt-1">${porcentagem}% das despesas</span>
                        </div>
                    `;
                });
        }

        // --- IMPORTAÇÃO INTELIGENTE DO NUBANK ---
        function importarNubank(event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                const text = e.target.result;
                const linhas = text.split('\n').map(l => l.trim()).filter(l => l); // Pega linhas não vazias
                
                if (linhas.length === 0) {
                    alert("Arquivo Vazio!"); return;
                }

                const cabecalho = linhas[0].toLowerCase();
                let importadas = 0;

                // Identifica se é Extrato da Conta ou Fatura do Cartão
                if (cabecalho.includes('identificador') || cabecalho.includes('data,valor')) {
                    // É CONTA CORRENTE NUBANK (Data, Valor, Identificador, Descrição)
                    for (let i = 1; i < linhas.length; i++) {
                        const colunas = linhas[i].split(',');
                        if (colunas.length >= 2) {
                            // Nubank conta data é DD/MM/YYYY
                            const dataBR = colunas[0].split('/');
                            const dataIso = `${dataBR[2]}-${dataBR[1]}-${dataBR[0]}`; 
                            const valorFloat = parseFloat(colunas[1].replace('R$','').trim());
                            const descricao = colunas[3] ? colunas[3].replace(/"/g, '') : "Transferência";

                            if (!isNaN(valorFloat)) {
                                transacoes.push({
                                    id: Date.now() + i,
                                    tipo: valorFloat > 0 ? 'entrada' : 'saida',
                                    nome: descricao,
                                    valor: Math.abs(valorFloat),
                                    data: dataIso,
                                    categoria: valorFloat > 0 ? 'Renda/Transferência' : '💳 Importado Nubank'
                                });
                                importadas++;
                            }
                        }
                    }
                } else if (cabecalho.includes('category') || cabecalho.includes('title')) {
                    // É CARTÃO DE CRÉDITO NUBANK (date, category, title, amount)
                    for (let i = 1; i < linhas.length; i++) {
                        const colunas = linhas[i].split(',');
                        if (colunas.length >= 4) {
                            // Nubank cartão data é YYYY-MM-DD
                            const dataIso = colunas[0];
                            const titulo = colunas[2].replace(/"/g, '');
                            const valorFloat = parseFloat(colunas[3]);

                            if (!isNaN(valorFloat)) {
                                transacoes.push({
                                    id: Date.now() + i,
                                    tipo: 'saida',
                                    nome: titulo,
                                    valor: valorFloat,
                                    data: dataIso,
                                    categoria: '💳 Importado Nubank'
                                });
                                importadas++;
                            }
                        }
                    }
                } else {
                    alert("Formato de CSV não reconhecido. Certifique-se de que é do Nubank.");
                    return;
                }

                atualizarInterface();
                alert(`Sucesso! ${importadas} transações foram importadas do Nubank.`);
            };
            
            reader.readAsText(file);
            event.target.value = ''; // Reseta o input de arquivo
        }

        // Inicia a aplicação na aba de resumo
        mudarAba('resumo');
    </script>
</body>
</html>
