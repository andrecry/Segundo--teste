
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador de Testes - Slots 3x3</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #1a1a1a;
            color: #fff;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        .painel {
            background-color: #2a2a2a;
            padding: 25px;
            border-radius: 15px;
            border: 4px solid #ffcc00;
            text-align: center;
            box-shadow: 0 0 20px rgba(255, 204, 0, 0.5);
            width: 90%;
            max-width: 360px;
        }
        #saldo-display {
            font-size: 1.4rem;
            font-weight: bold;
            margin-bottom: 15px;
            color: #ffcc00;
        }
        /* Configuração da Grade 3x3 */
        .rolos {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin: 20px 0;
        }
        .rolo {
            background-color: #fff;
            color: #000;
            font-size: 2.5rem;
            height: 80px;
            line-height: 80px;
            border-radius: 10px;
            border: 2px solid #888;
            user-select: none;
        }
        .girando {
            animation: tremer 0.1s infinite alternate;
        }
        @keyframes tremer {
            0% { transform: translateY(-3px); }
            100% { transform: translateY(3px); }
        }
        button {
            background-color: #ffcc00;
            color: #000;
            border: none;
            padding: 12px 35px;
            font-size: 1.2rem;
            font-weight: bold;
            border-radius: 5px;
            cursor: pointer;
            transition: 0.2s;
            box-shadow: 0 4px #b38f00;
            width: 100%;
        }
        button:hover {
            background-color: #e6b800;
        }
        button:active {
            box-shadow: 0 2px #b38f00;
            transform: translateY(2px);
        }
        button:disabled {
            background-color: #555;
            color: #888;
            box-shadow: none;
            cursor: not-allowed;
        }
        .status {
            margin-top: 20px;
            font-size: 1.2rem;
            font-weight: bold;
            min-height: 28px;
        }
    </style>
</head>
<body>

    <div class="painel">
        <h2>🎰 Slot Test 3x3</h2>
        <div id="saldo-display">Saldo: R$ 100.00</div>
        
        <div class="rolos">
            <div id="rolo1" class="rolo">🐯</div>
            <div id="rolo2" class="rolo">🐯</div>
            <div id="rolo3" class="rolo">🐯</div>
            <div id="rolo4" class="rolo">🐯</div>
            <div id="rolo5" class="rolo">🐯</div>
            <div id="rolo6" class="rolo">🐯</div>
            <div id="rolo7" class="rolo">🐯</div>
            <div id="rolo8" class="rolo">🐯</div>
            <div id="rolo9" class="rolo">🐯</div>
        </div>
        
        <button id="btn-girar" onclick="jogar()">GIRAR (R$ 2.00)</button>
        <div id="status" class="status"></div>
    </div>

    <script>
        let saldo = 100.00;
        const custoAposta = 2.00;
        const premio = 15.00;
        
        const simbolos = ['🐯', '💎', '🍊', '🔔', '🪙'];
        const CHANCE_DE_VITORIA = 0.4; // 40% de chance de forçar linha vencedora

        // Mapeamento dos 9 elementos da tela
        const rolosIds = ['rolo1', 'rolo2', 'rolo3', 'rolo4', 'rolo5', 'rolo6', 'rolo7', 'rolo8', 'rolo9'];
        const statusDiv = document.getElementById('status');
        const btnGirar = document.getElementById('btn-girar');

        function atualizarTela() {
            document.getElementById('saldo-display').innerText = `Saldo: R$ ${saldo.toFixed(2)}`;
        }

        function obterSimboloAleatorio() {
            return simbolos[Math.floor(Math.random() * simbolos.length)];
        }

        function jogar() {
            if (saldo < custoAposta) {
                statusDiv.style.color = '#ff3333';
                statusDiv.innerText = "Saldo insuficiente para testar!";
                return;
            }

            btnGirar.disabled = true;
            saldo -= custoAposta;
            atualizarTela();
            
            statusDiv.style.color = '#fff';
            statusDiv.innerText = "Girando...";
            
            // Ativa animação nos 9 rolos
            rolosIds.forEach(id => document.getElementById(id).classList.add('girando'));

            const loopAnimacao = setInterval(() => {
                rolosIds.forEach(id => {
                    document.getElementById(id).innerText = obterSimboloAleatorio();
                });
            }, 50);

            // Geração dos resultados para a matriz 3x3
            let resultados = [];
            const seedSorteio = Math.random();

            if (seedSorteio < CHANCE_DE_VITORIA) {
                // Força vitória preenchendo uma linha aleatória com símbolos iguais
                const simboloVencedor = obterSimboloAleatorio();
                for(let i = 0; i < 9; i++) {
                    resultados.push(obterSimboloAleatorio());
                }
                // Garante a linha do meio (indices 3, 4, 5) com o mesmo símbolo
                resultados[3] = simboloVencedor;
                resultados[4] = simboloVencedor;
                resultados[5] = simboloVencedor;
            } else {
                // Preenchimento completamente aleatório
                for(let i = 0; i < 9; i++) {
                    resultados.push(obterSimboloAleatorio());
                }
                // Evita ganho acidental na linha do meio se der igual
                if (resultados[3] === resultados[4] && resultados[4] === resultados[5]) {
                    resultados[5] = simbolos[(simbolos.indexOf(resultados[5]) + 1) % simbolos.length];
                }
            }

            setTimeout(() => {
                clearInterval(loopAnimacao);
                
                // Para a animação e define valores finais
                rolosIds.forEach((id, index) => {
                    const el = document.getElementById(id);
                    el.classList.remove('girando');
                    el.innerText = resultados[index];
                });

                // Verifica se a linha do meio é vencedora
                if (resultados[3] === resultados[4] && resultados[4] === Play = resultados[5]) {
                    saldo += premio;
                    statusDiv.style.color = '#33ff33';
                    statusDiv.innerText = `Linha Fechada! +R$ ${premio.toFixed(2)}`;
                } else {
                    statusDiv.style.color = '#ff3333';
                    statusDiv.innerText = "Tente de novo!";
                }

                atualizarTela();
                btnGirar.disabled = false;
            }, 800);
        }
    </script>
</body>
</html>
