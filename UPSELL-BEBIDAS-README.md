# Sistema de Up-Sell de Bebidas - Pepper's Pizzaria

## 📋 Resumo das Implementações

Este documento descreve as melhorias implementadas no agente de atendimento da Pepper's Pizzaria, incluindo o sistema completo de up-sell de bebidas.

---

## 🥤 Bebidas Adicionadas

### Catálogo Completo de Bebidas:

1. **Coca-Cola 2L Original** - R$ 15,90
2. **Coca-Cola 2L Zero** - R$ 15,90
3. **Coca-Cola 1,5L Original** - R$ 12,90
4. **Coca-Cola 1,5L Zero** - R$ 12,90
5. **Guaraná Kuat 2L** - R$ 11,00
6. **Mate Couro 1L** - R$ 9,00

---

## 🎯 Sistema de Up-Sell Estratégico

### Momento Exato de Oferta
O agente oferece bebidas **IMEDIATAMENTE após confirmar o pedido de pizza** e **ANTES de perguntar sobre mais alguma coisa**.

### 3 Estratégias de Oferta

#### 1. Oferta Econômica (Pedidos Pequenos)
**Quando usar:** Cliente pediu 1 pizza de 6 fatias

```
Que tal uma bebida pra acompanhar? 🥤
• Mate Couro 1L — R$ 9,00
• Guaraná Kuat 2L — R$ 11,00
```

#### 2. Oferta Completa (Pedidos Grandes)
**Quando usar:** Cliente pediu 2+ pizzas OU pizza 10 fatias

```
Quer uma bebida gelada pra acompanhar? 🥤

💧 REFRIGERANTES:
1️⃣ Coca-Cola 2L Original — R$ 15,90
2️⃣ Coca-Cola 2L Zero — R$ 15,90
3️⃣ Coca-Cola 1,5L Original — R$ 12,90
4️⃣ Coca-Cola 1,5L Zero — R$ 12,90
5️⃣ Guaraná Kuat 2L — R$ 11,00
6️⃣ Mate Couro 1L — R$ 9,00

Quer adicionar alguma?
```

#### 3. Oferta Simples (Cliente Objetivo)
**Quando usar:** Cliente demonstra pressa ou objetividade

```
Bebida pra acompanhar? 🥤
Temos Coca-Cola, Guaraná Kuat e Mate Couro!
```

### Regras do Up-Sell

✅ **OBRIGATÓRIO:**
- Oferecer bebidas uma única vez
- Oferecer APÓS confirmar pizza
- Oferecer ANTES de perguntar sobre outros itens
- Aguardar resposta do cliente

❌ **PROIBIDO:**
- Pular a etapa de up-sell
- Oferecer antes de confirmar a pizza
- Oferecer depois de perguntar tipo de entrega
- Repetir oferta se cliente já respondeu
- Assumir que cliente quer bebida sem confirmação
- Calcular valores de bebida manualmente
- Inventar bebidas ou preços

---

## 🔧 Melhorias no Sistema de Ferramentas

### 1. CALCULADORA
**Uso OBRIGATÓRIO para:**
- Multiplicar preço × quantidade de cada item
- Somar subtotal de pizzas + bebidas
- Adicionar taxa de entrega
- Calcular troco

**Exemplo de Uso Correto:**
```
Cenário: 1 Pizza 8 fatias (R$49,90) + 2 Coca-Cola 2L (R$15,90)

1. CALCULADORA: 49.90 * 1 = 49.90
2. CALCULADORA: 15.90 * 2 = 31.80
3. CALCULADORA: 49.90 + 31.80 = 81.70
4. CALCULADORA: 81.70 + 7.50 = 89.20 (com entrega)
```

### 2. FRETE
**Parâmetros Obrigatórios:**
```json
{
  "rua": "Extrair da mensagem",
  "numero": "Extrair da mensagem",
  "bairro": "Extrair da mensagem",
  "nome_do_cliente": "Nome coletado",
  "remotejid": "$('Dados').item.json.Telefone",
  "frete": "frete"
}
```

**Proibições:**
- ❌ Usar valor fixo de frete (R$ 5,00)
- ❌ Assumir valor sem chamar ferramenta
- ✅ SEMPRE usar ferramenta FRETE para calcular

### 3. ATUALIZA_ENDERECO
- Executar IMEDIATAMENTE após FRETE
- Salva endereço no banco de dados

### 4. ATUALIZA_PEDIDO
**Executar ANTES da confirmação final**

**Exemplo de JSON Correto:**
```json
{
  "itens": [
    {
      "tipo": "pizza",
      "sabor": "Calabresa",
      "tamanho": "8 fatias",
      "quantidade": 1,
      "valor_unitario": 49.90,
      "valor_total": 49.90
    },
    {
      "tipo": "bebida",
      "nome": "Coca-Cola 2L Original",
      "quantidade": 2,
      "valor_unitario": 15.90,
      "valor_total": 31.80
    }
  ],
  "subtotal": 81.70,
  "taxa_entrega": 7.50,
  "total": 89.20,
  "forma_pagamento": "dinheiro",
  "troco_para": 100.00,
  "tipo_entrega": "entrega",
  "endereco": {
    "rua": "Rua das Flores",
    "numero": "123",
    "bairro": "Centro",
    "complemento": ""
  },
  "nome_cliente": "João Silva",
  "observacoes": ""
}
```

---

## 📊 Fluxo Completo Atualizado

```
1. Saudação + Envio de Imagens do Cardápio
   ↓
2. Coleta do Pedido de Pizza
   ↓
3. ✨ UP-SELL DE BEBIDAS (NOVO)
   ↓
4. Finalizar Coleta ("Mais alguma coisa?")
   ↓
5. Tipo de Entrega (Entrega/Retirada)
   ↓
6. Endereço (se entrega) → FRETE → ATUALIZA_ENDERECO
   ↓
7. Forma de Pagamento
   ↓
8. Nome do Cliente
   ↓
9. Cálculos Finais (CALCULADORA)
   ↓
10. ATUALIZA_PEDIDO
    ↓
11. Confirmação Final
```

---

## ✅ Checklist Antes da Confirmação Final

- [ ] Todos os itens coletados e confirmados?
- [ ] Up-sell de bebidas oferecido?
- [ ] Tipo de entrega definido?
- [ ] Se entrega: FRETE calculado + ATUALIZA_ENDERECO executado?
- [ ] Forma de pagamento definida?
- [ ] Nome do cliente coletado?
- [ ] TODOS os cálculos via CALCULADORA?
- [ ] ATUALIZA_PEDIDO executado?
- [ ] Confirmação final completa?

---

## 🎯 Exemplo de Atendimento Completo

**Cliente:** "Quero uma pizza calabresa 8 fatias"

**Agente:** "Perfeito! Anotei 🍕"

**Agente:** "Que tal uma bebida pra acompanhar? 🥤
• Mate Couro 1L — R$ 9,00
• Guaraná Kuat 2L — R$ 11,00"

**Cliente:** "Quero 2 Coca-Cola 2L"

**Agente:** "Ótimo! Anotei 2 Coca-Cola 2L Original 🥤
Mais alguma coisa? 🍕"

**Cliente:** "Não, é só isso"

**Agente:** "Vai ser entrega 🏠 ou retirada 📍?"

**Cliente:** "Entrega"

**Agente:** "Qual o endereço completo? 📍
• Rua e número
• Bairro
• Complemento (se houver)"

**Cliente:** "Rua das Flores, 123, Centro"

[Agente executa FRETE]

**Agente:** "Frete para seu endereço: R$ 7,50 🏠
Como vai ser o pagamento? 💳 Cartão | 💵 Dinheiro | 📱 Pix"

**Cliente:** "Dinheiro"

**Agente:** "Vai precisar de troco? Pra quanto? 💵"

**Cliente:** "Sim, pra 100"

**Agente:** "Qual seu nome? 😊"

**Cliente:** "João Silva"

[Agente executa cálculos via CALCULADORA]
[Agente executa ATUALIZA_PEDIDO]

**Agente:**
```
Pedido confirmado, João! 🍕✅

🍕 SEU PEDIDO:
• 1x Pizza Calabresa 8 fatias — R$ 49,90
• 2x Coca-Cola 2L Original — R$ 31,80

💰 VALORES:
Subtotal: R$ 81,70
+ Entrega: R$ 7,50
TOTAL: R$ 89,20 ✅

🏠 ENTREGA EM:
Rua das Flores, 123 - Centro
Prazo: 40-60 min ⏰

💳 PAGAMENTO:
Dinheiro
Troco para: R$ 100,00
Troco: R$ 10,80

Obrigado por escolher a Pepper's! 🍕❤️
```

---

## 🚫 Proibições Críticas

1. ❌ NUNCA calcular manualmente - SEMPRE usar CALCULADORA
2. ❌ NUNCA usar valor fixo de frete - SEMPRE chamar FRETE
3. ❌ NUNCA pular up-sell de bebidas
4. ❌ NUNCA confirmar pedido sem ATUALIZA_PEDIDO
5. ❌ NUNCA enviar JSON inválido
6. ❌ NUNCA inventar bebidas ou preços
7. ❌ NUNCA assumir dados sem perguntar
8. ❌ NUNCA pular etapas do fluxo

---

## 📝 Notas Técnicas

- **Protocolo:** 58211-UPSELL-V2
- **Temperatura do modelo:** 0.2 (precisão máxima)
- **Máximo de iterações:** 30
- **Memória de contexto:** 10 mensagens (PostgreSQL)

---

## 🎓 Tratamento de Edge Cases

### Cliente muda pedido
✅ Confirmar mudança, recalcular TUDO, atualizar

### Cliente cancela item
✅ Remover item, recalcular total

### Cliente não quer bebida
✅ Aceitar, não insistir, avançar

### Endereço incompleto
✅ Pedir informações faltantes especificamente

### Erro ao chamar ferramenta
✅ Informar educadamente, tentar novamente

---

## 📊 Valores de Referência

### Pizzas Tradicionais
- 6 fatias: R$ 42,00
- 8 fatias: R$ 49,90
- 10 fatias: R$ 59,90

### Pizzas Especiais
- 6 fatias: R$ 46,90
- 8 fatias: R$ 59,90
- 10 fatias: R$ 69,90

### Pizzas Doces
- 4 fatias: R$ 46,90
- 6 fatias: R$ 59,90
- 8 fatias: R$ 69,90

### Combos
- **Combo 6 fatias:** Pizza 6 fatias + 1L Mate Couro = R$ 55,90
- **Combo 10 fatias:** Pizza 10 fatias + 2L Mate Couro = R$ 59,90

### Adicionais
- Borda recheada: R$ 9,90

---

## ✨ Melhorias Implementadas

1. ✅ Sistema completo de up-sell de bebidas
2. ✅ 3 estratégias de oferta baseadas no contexto
3. ✅ Catálogo de 6 bebidas com preços
4. ✅ Instruções detalhadas de uso de ferramentas
5. ✅ Validações rigorosas em cada etapa
6. ✅ Exemplos práticos de execução correta
7. ✅ Tratamento de erros e edge cases
8. ✅ Checklist de validação pré-confirmação
9. ✅ Regras claras para cálculo de frete dinâmico
10. ✅ Formato JSON estruturado para ATUALIZA_PEDIDO

---

**Arquivo gerado:** `workflow-atendente-upsell-bebidas.json`
**Data:** 2025-11-15
**Versão do Protocolo:** 58211-UPSELL-V2
