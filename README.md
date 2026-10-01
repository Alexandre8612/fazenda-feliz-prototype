# Fazenda Feliz - Projeto

## Visão geral
Um jogo de fazenda em estilo web com elementos de economia, progresso em níveis e loja.

## Como rodar localmente
```bash
python -m http.server 8000
```
Depois abra em:
```text
http://localhost:8000
```

## Estrutura da base do jogo
- `index.html` - login/cadastro
- `jogo.html` - gameplay
- `MONETIZACAO_COMPLETA.md` - orientações financeiras e compliance

## Próximo passo recomendado
Antes de liberar monetização real:
1. validar o modelo com contador/advogado;
2. configurar gateway de pagamento;
3. implementar backend de saldo e saques;
4. testar fluxos financeiros em ambiente sandbox.
