# Workflow n8n - Cálculo de Frete por Endereço

Sistema de cálculo de frete para entregas em Montes Claros, MG.

## 📋 Descrição

Este workflow n8n recebe um endereço do cliente (rua, número e bairro) e retorna o valor do frete calculado baseado na tabela de bairros de Montes Claros.

## 🚀 Como Importar o Workflow no n8n

1. Abra seu n8n
2. Clique em **"Workflows"** no menu lateral
3. Clique no botão **"+"** para criar novo workflow
4. Clique nos 3 pontinhos (⋮) no canto superior direito
5. Selecione **"Import from File"**
6. Escolha o arquivo: `workflows/calcular-frete-endereco.json`
7. Clique em **"Save"** para salvar o workflow

## 🎯 Como Funciona

O workflow contém 5 nodes:

1. **Webhook - Receber Endereço**: Recebe os dados via POST
2. **Validar Endereço**: Valida e normaliza os dados recebidos
3. **Calcular Frete por Bairro**: Consulta a tabela de frete e calcula o valor
4. **Set - Retorno de Sucesso**: Formata a resposta final
5. **Webhook Response**: Retorna o resultado em JSON

## 📥 Como Usar

### Ativar o Workflow

1. Abra o workflow importado
2. Clique em **"Active"** no canto superior direito
3. Copie a URL do webhook que aparecerá no node "Webhook - Receber Endereço"

### Fazer Requisição

**Endpoint:** (URL do webhook gerado)

**Método:** POST

**Body (JSON):**
```json
{
  "rua": "Avenida Doutor Ruy Braga",
  "numero": "123",
  "bairro": "Centro"
}
```

### Exemplo de Resposta

```json
{
  "sucesso": true,
  "mensagem": "Frete calculado com sucesso! Valor: R$ 10.00",
  "endereco_completo": "Avenida Doutor Ruy Braga, 123 - Centro, Montes Claros/MG",
  "bairro": "Centro",
  "cidade": "Montes Claros",
  "estado": "MG",
  "valor_frete": 10
}
```

## 💰 Tabela de Frete Atual

### Zona Central (R$ 10,00 - R$ 12,00)
- Centro: R$ 10,00
- Vila Guilhermina: R$ 10,00
- Todos os Santos: R$ 12,00
- São José: R$ 12,00
- Morrinhos: R$ 12,00

### Zona Norte (R$ 15,00 - R$ 18,00)
- Major Prates: R$ 15,00
- Jardim Panorama: R$ 15,00
- São Luiz: R$ 15,00
- Ibituruna: R$ 18,00

### Zona Sul (R$ 15,00 - R$ 18,00)
- Santo Expedito: R$ 15,00
- São Judas Tadeu: R$ 15,00
- Cidade Nova: R$ 15,00
- Independência: R$ 15,00
- Melo: R$ 18,00

### Zona Leste (R$ 18,00 - R$ 20,00)
- Alterosa: R$ 18,00
- Jardim Alterosa: R$ 18,00
- Planalto: R$ 20,00
- Ciro dos Anjos: R$ 20,00

### Zona Oeste (R$ 18,00 - R$ 20,00)
- Maracanã: R$ 18,00
- Sumaré: R$ 18,00
- Canelas: R$ 20,00
- Santa Rita: R$ 20,00
- Vila Atlético: R$ 18,00

### Bairros Distantes (R$ 22,00 - R$ 25,00)
- Distrito Industrial: R$ 25,00
- Village do Lago: R$ 25,00
- Santa Eugênia: R$ 22,00
- Jardim Brasil: R$ 22,00
- Roxo Verde: R$ 22,00

**Bairros não cadastrados:** R$ 20,00 (valor padrão)

## ⚙️ Customização

### Alterar Valores de Frete

1. Abra o workflow no n8n
2. Clique no node **"Calcular Frete por Bairro"**
3. Edite a variável `tabelaFrete` no código JavaScript
4. Adicione novos bairros ou altere os valores existentes
5. Salve o workflow

### Exemplo de Customização

```javascript
const tabelaFrete = {
  'centro': 10.00,
  'seu_bairro': 15.00,  // Adicione novos bairros aqui
  // ...
};
```

## 🧪 Testar o Workflow

### Usando cURL

```bash
curl -X POST "URL_DO_WEBHOOK" \
  -H "Content-Type: application/json" \
  -d '{
    "rua": "Avenida Doutor Ruy Braga",
    "numero": "123",
    "bairro": "Centro"
  }'
```

### Usando Postman/Insomnia

1. Crie uma nova requisição POST
2. Cole a URL do webhook
3. Em Body, selecione "raw" e "JSON"
4. Cole o JSON de exemplo
5. Clique em "Send"

## 🔧 Integração

Este workflow pode ser integrado com:

- ✅ WhatsApp Business (via Evolution API, Baileys, etc.)
- ✅ Chatbots
- ✅ Sistemas de e-commerce
- ✅ Aplicativos mobile
- ✅ Sites/Landing pages
- ✅ Outros workflows n8n

## 📝 Validações

O workflow valida:
- ✅ Presença de rua, número e bairro
- ✅ Normalização de texto (remove acentos, padroniza)
- ✅ Retorna erro se dados estiverem incompletos

## 🐛 Tratamento de Erros

Se o endereço estiver incompleto:

```json
{
  "sucesso": false,
  "erro": "Dados incompletos. Envie: rua, numero e bairro",
  "mensagem": "Por favor, informe o endereço completo: rua, número e bairro"
}
```

## 📞 Suporte

Para adicionar mais bairros ou alterar valores, edite o node "Calcular Frete por Bairro" dentro do workflow n8n.

---

**Desenvolvido para Montes Claros, MG - Brasil** 🇧🇷
