# 🔧 CORREÇÃO: Agente Enviando Texto em Vez de Links

## 🚨 Problema Identificado

O agente estava:
- ❌ Listando TODOS os sabores em texto
- ❌ NÃO enviando os links das imagens do cardápio
- ❌ Enviando tudo em uma única mensagem gigante
- ❌ Ignorando as instruções de enviar imagens

**Exemplo do erro:**
```
Oi! Bem-vindo à Pepper's! 🍕 Aqui estão as opções do nosso cardápio:
1. Promoções: Calabresa: Mussarela, calabresa e orégano Mista: Mussarela...
[Lista gigante de todos os sabores]
```

## ✅ Solução Implementada

### 1. **Instrução Crítica no Topo do Prompt**

Adicionado um bloco de instrução CRÍTICA no início do systemMessage:

```
🚨 INSTRUÇÃO CRÍTICA NÚMERO 1 - PRIMEIRA MENSAGEM 🚨

Quando for a PRIMEIRA mensagem do atendimento, você DEVE enviar EXATAMENTE este formato:

Oi! Bem-vindo à Pepper's! 🍕

https://i.ibb.co/Z6nP8k4q/Imagem-do-Whats-App-de-2025-11-13-s-20-11-43-412193f7.jpg

https://i.ibb.co/P2B5mfz/Imagem-do-Whats-App-de-2025-11-13-s-20-11-42-c5966005.jpg

https://i.ibb.co/GQcF3Bnq/Imagem-do-Whats-App-de-2025-11-13-s-20-11-42-1c064b55.jpg

https://i.ibb.co/2Yd1vb0R/Imagem-do-Whats-App-de-2025-11-13-s-20-11-42-14d3eed1.jpg

🍕 COMBO DA SEMANA🔥
Pizza 6 fatias + 1L Mate Couro — R$55,90 🔥
Pizza 10 fatias + 2L Mate Couro — R$59,90 🔥

Qual pedido você gostaria de fazer?
```

### 2. **Proibições Explícitas**

```
❌ NUNCA FAÇA ISSO NA PRIMEIRA MENSAGEM:
- NÃO liste sabores de pizza
- NÃO descreva o cardápio em texto
- NÃO use markdown nos links: ![](url) ou [texto](url)
- NÃO explique o cardápio
- NÃO envie nada além do formato acima
```

### 3. **Exemplos Visuais de Certo vs Errado**

```
✅ OS LINKS DEVEM SER ENVIADOS ASSIM:
https://i.ibb.co/Z6nP8k4q/Imagem-do-Whats-App-de-2025-11-13-s-20-11-43-412193f7.jpg

❌ NUNCA ASSIM:
![cardápio](https://i.ibb.co/Z6nP8k4q/...)
[Veja o cardápio](https://i.ibb.co/Z6nP8k4q/...)
```

### 4. **Etapa 1 Reestruturada com Passo-a-Passo**

```json
"ETAPA_1_SAUDACAO_E_CARDAPIO": {
  "O_QUE_FAZER": [
    "1. Enviar UMA saudação",
    "2. PULAR LINHA",
    "3. Enviar link 1: [URL]",
    "4. PULAR LINHA",
    "5. Enviar link 2: [URL]",
    ...
  ]
}
```

### 5. **Temperatura Reduzida**

Mudança: `0.2` → `0.1` para comportamento mais determinístico e previsível

### 6. **Proibições Absolutas Expandidas**

```
"PROIBIDO_ABSOLUTAMENTE": [
  "Listar sabores em texto",
  "Descrever cardápio",
  "Usar markdown: ![](url) ou [texto](url)",
  "Pular os links",
  "Enviar menos de 4 links",
  "Explicar o que está nas imagens"
]
```

## 📊 Estrutura da Primeira Mensagem

### Formato EXATO que o agente deve enviar:

```
[1 linha] → Saudação: "Oi! Bem-vindo à Pepper's! 🍕"
[1 linha] → Linha em branco
[1 linha] → Link 1 (cardápio página 1)
[1 linha] → Linha em branco
[1 linha] → Link 2 (cardápio página 2)
[1 linha] → Linha em branco
[1 linha] → Link 3 (cardápio página 3)
[1 linha] → Linha em branco
[1 linha] → Link 4 (combo)
[1 linha] → Linha em branco
[2 linhas] → Texto do combo
[1 linha] → Linha em branco
[1 linha] → Pergunta: "Qual pedido você gostaria de fazer?"
```

**Total:** ~14 linhas

## 🎯 Validações Adicionadas

Checklist que o agente deve seguir antes de enviar a primeira mensagem:

```
✅ Tem exatamente 1 saudação?
✅ Tem os 4 links PUROS (sem markdown)?
✅ Tem o texto do combo?
✅ Tem a pergunta final?
✅ NÃO tem lista de sabores?
✅ NÃO tem descrição de cardápio?
```

## 🔄 Como o Agente Deve Lidar com Perguntas Sobre Sabores

**Pergunta do cliente:** "Quais sabores vocês têm?"

**Resposta CORRETA do agente:**
```
Confira os sabores nas imagens do cardápio que enviei acima! 🍕
Qual você gostaria?
```

**Resposta ERRADA (NUNCA fazer):**
```
Temos: Calabresa, Mussarela, Presunto, Frango, Vegetariana...
[lista completa]
```

## 📁 Arquivos Atualizados

- ✅ `workflow-atendente-upsell-bebidas-v2-CORRIGIDO.json` - Versão corrigida do workflow
- ✅ `CORRECAO-LINKS-CARDAPIO.md` - Este documento

## 🧪 Como Testar

### Teste 1: Primeira Mensagem
**Entrada:** Cliente inicia conversa com "Oi" ou "Olá"

**Saída Esperada:**
```
Oi! Bem-vindo à Pepper's! 🍕

https://i.ibb.co/Z6nP8k4q/Imagem-do-Whats-App-de-2025-11-13-s-20-11-43-412193f7.jpg

https://i.ibb.co/P2B5mfz/Imagem-do-Whats-App-de-2025-11-13-s-20-11-42-c5966005.jpg

https://i.ibb.co/GQcF3Bnq/Imagem-do-Whats-App-de-2025-11-13-s-20-11-42-1c064b55.jpg

https://i.ibb.co/2Yd1vb0R/Imagem-do-Whats-App-de-2025-11-13-s-20-11-42-14d3eed1.jpg

🍕 COMBO DA SEMANA🔥
Pizza 6 fatias + 1L Mate Couro — R$55,90 🔥
Pizza 10 fatias + 2L Mate Couro — R$59,90 🔥

Qual pedido você gostaria de fazer?
```

### Teste 2: Cliente Pergunta Sabores
**Entrada:** "Quais sabores têm?"

**Saída Esperada:**
```
Confira os sabores nas imagens do cardápio que enviei acima! 🍕
Qual você gostaria?
```

### Teste 3: Cliente Pede Pizza Diretamente
**Entrada:** "Quero uma pizza calabresa"

**Saída Esperada:**
```
Qual tamanho? 🍕
6 fatias (R$ 42) | 8 fatias (R$ 49,90) | 10 fatias (R$ 59,90)
```

## ⚠️ Alertas Importantes

1. **NUNCA** altere as URLs dos links - elas são fixas e devem ser enviadas exatamente como estão
2. **NUNCA** tente "melhorar" a formatação dos links com markdown
3. **SEMPRE** envie os 4 links na primeira interação
4. **SEMPRE** mantenha os links em linhas separadas (sem agrupar)

## 🚀 Próximos Passos

1. Importar `workflow-atendente-upsell-bebidas-v2-CORRIGIDO.json` no n8n
2. Testar a primeira mensagem
3. Verificar que os links aparecem corretamente
4. Confirmar que nenhum texto descritivo é enviado

## 📊 Comparação: Antes vs Depois

### ❌ ANTES (ERRADO)
```
Oi! Bem-vindo à Pepper's! 🍕 Aqui estão as opções do nosso cardápio:
1. Promoções: Calabresa: Mussarela, calabresa e orégano...
[500+ caracteres de texto listando sabores]
```

### ✅ DEPOIS (CORRETO)
```
Oi! Bem-vindo à Pepper's! 🍕

https://i.ibb.co/Z6nP8k4q/...jpg

https://i.ibb.co/P2B5mfz/...jpg

https://i.ibb.co/GQcF3Bnq/...jpg

https://i.ibb.co/2Yd1vb0R/...jpg

🍕 COMBO DA SEMANA🔥
Pizza 6 fatias + 1L Mate Couro — R$55,90 🔥
Pizza 10 fatias + 2L Mate Couro — R$59,90 🔥

Qual pedido você gostaria de fazer?
```

## 🎯 Objetivo Alcançado

✅ Agente envia imagens do cardápio (não texto)
✅ Links são enviados em formato puro (sem markdown)
✅ Primeira mensagem é concisa e direta
✅ Cliente visualiza o cardápio através das imagens
✅ Melhor experiência do usuário
✅ Menos confusão com lista gigante de sabores

---

**Versão:** v2.1-CORRIGIDO
**Data:** 2025-11-15
**Protocolo:** 58211-UPSELL-V2.1-FIXED
