# Workflow n8n - Cálculo de Frete por Distância (Raio)

Sistema de cálculo de frete baseado na distância real (latitude/longitude) para entregas em Montes Claros, MG.

## 📋 Descrição

Este workflow n8n recebe um endereço do cliente (rua, número e bairro) e retorna o valor do frete calculado baseado na **distância real** entre sua loja e o endereço do cliente usando geolocalização.

## 🎯 Como Funciona

O workflow utiliza **7 nodes conectados**:

1. **Webhook - Receber Endereço**: Recebe POST com `rua`, `numero`, `bairro`
2. **Validar Endereço**: Valida e formata os dados do endereço
3. **Buscar Coordenadas (Geocoding)**: Converte o endereço em latitude/longitude usando API Nominatim (OpenStreetMap)
4. **Calcular Distância (Haversine)**: Calcula a distância em km usando fórmula de Haversine
5. **Calcular Frete por Distância**: Aplica tabela de preços baseada na distância
6. **Set - Retorno de Sucesso**: Formata resposta estruturada ✅
7. **Webhook Response**: Retorna resultado em JSON

## 🌍 Tecnologias Utilizadas

- **Geocoding API**: Nominatim (OpenStreetMap) - **GRÁTIS**, sem necessidade de API key
- **Cálculo de Distância**: Fórmula de Haversine (precisão geográfica)
- **Automação**: n8n workflow

## 🚀 Instalação

### 1. Importar no n8n

1. Abra seu n8n
2. Clique em **"Workflows"** no menu lateral
3. Clique no botão **"+"** para criar novo workflow
4. Clique nos 3 pontinhos (⋮) no canto superior direito
5. Selecione **"Import from File"**
6. Escolha o arquivo: `workflows/calcular-frete-endereco.json`
7. Clique em **"Save"**

### 2. Configurar Coordenadas da Origem

**IMPORTANTE**: Configure as coordenadas da sua loja/depósito!

1. Abra o workflow importado
2. Clique no node **"Calcular Distância (Haversine)"**
3. Localize estas linhas no código:

```javascript
// ⚙️ CONFIGURE AQUI AS COORDENADAS DA SUA LOJA/ORIGEM
const ORIGEM_LATITUDE = -16.7285;  // ← ALTERE AQUI
const ORIGEM_LONGITUDE = -43.8621; // ← ALTERE AQUI
```

4. Substitua pelos valores da sua loja
5. Salve o workflow

**Como descobrir as coordenadas da sua loja:**
- Abra o Google Maps
- Clique com botão direito no local da sua loja
- Copie as coordenadas (primeiro número = latitude, segundo = longitude)

### 3. Ativar o Workflow

1. Clique em **"Active"** no canto superior direito
2. Copie a URL do webhook que aparecerá

## 📥 Como Usar

### Fazer Requisição

**Endpoint:** (URL do webhook gerado pelo n8n)

**Método:** POST

**Body (JSON):**
```json
{
  "rua": "Avenida Doutor Ruy Braga",
  "numero": "123",
  "bairro": "Centro"
}
```

### Exemplo de Resposta de Sucesso

```json
{
  "sucesso": true,
  "mensagem": "Frete calculado com sucesso! Valor: R$ 10.00",
  "endereco_completo": "Avenida Doutor Ruy Braga, 123 - Centro, Montes Claros/MG",
  "bairro": "Centro",
  "cidade": "Montes Claros",
  "estado": "MG",
  "valor_frete": 10,
  "distancia_km": 1.5,
  "faixa_distancia": "Até 2 km"
}
```

### Exemplo de Resposta de Erro

```json
{
  "sucesso": false,
  "erro": "Endereço não encontrado",
  "mensagem": "Não foi possível encontrar as coordenadas deste endereço. Verifique se o endereço está correto.",
  "endereco_fornecido": "Rua Inexistente, 999 - Bairro Teste, Montes Claros/MG"
}
```

## 💰 Tabela de Frete Padrão (Por Distância)

| Distância | Valor | Descrição |
|-----------|-------|-----------|
| Até 2 km | R$ 10,00 | Região central |
| 2 a 5 km | R$ 15,00 | Próximo |
| 5 a 8 km | R$ 20,00 | Médio |
| 8 a 12 km | R$ 25,00 | Distante |
| 12 a 15 km | R$ 30,00 | Muito distante |
| 15 a 20 km | R$ 35,00 | Extremo |
| Acima de 20 km | R$ 40,00 | Fora da cidade |

## ⚙️ Customização

### Alterar Tabela de Preços

1. Abra o workflow no n8n
2. Clique no node **"Calcular Frete por Distância"**
3. Edite a variável `tabelaFretePorDistancia`:

```javascript
const tabelaFretePorDistancia = [
  { ate_km: 2, valor: 10.00, descricao: 'Até 2 km' },
  { ate_km: 5, valor: 15.00, descricao: '2 a 5 km' },
  // Adicione ou modifique as faixas aqui
];
```

4. Salve o workflow

### Alterar Coordenadas de Origem

Edite o node **"Calcular Distância (Haversine)"** conforme instruções acima.

## 🧪 Testes

### Teste 1: Endereço no Centro

```bash
curl -X POST "URL_DO_WEBHOOK" \
  -H "Content-Type: application/json" \
  -d '{
    "rua": "Avenida Doutor Ruy Braga",
    "numero": "123",
    "bairro": "Centro"
  }'
```

### Teste 2: Endereço Distante

```bash
curl -X POST "URL_DO_WEBHOOK" \
  -H "Content-Type: application/json" \
  -d '{
    "rua": "Avenida Zelinda Fonseca",
    "numero": "500",
    "bairro": "Ibituruna"
  }'
```

### Teste 3: Endereço Inválido (deve retornar erro)

```bash
curl -X POST "URL_DO_WEBHOOK" \
  -H "Content-Type: application/json" \
  -d '{
    "rua": "Rua Inexistente",
    "numero": "999",
    "bairro": "Bairro Teste"
  }'
```

## 🔧 Integração

Este workflow pode ser integrado com:

- ✅ WhatsApp Business (Evolution API, Baileys, etc.)
- ✅ Chatbots (Dialogflow, Botpress, etc.)
- ✅ Sistemas de e-commerce (WooCommerce, Shopify, etc.)
- ✅ Aplicativos mobile (React Native, Flutter, etc.)
- ✅ Sites/Landing pages
- ✅ Outros workflows n8n
- ✅ Zapier, Make (Integromat), etc.

## 📊 Informações Retornadas

O workflow retorna:

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `sucesso` | boolean | Se o cálculo foi bem-sucedido |
| `mensagem` | string | Mensagem descritiva do resultado |
| `endereco_completo` | string | Endereço formatado |
| `bairro` | string | Nome do bairro |
| `cidade` | string | Nome da cidade (sempre Montes Claros) |
| `estado` | string | Sigla do estado (MG) |
| `valor_frete` | number | Valor do frete em reais |
| `distancia_km` | number | Distância calculada em quilômetros |
| `faixa_distancia` | string | Descrição da faixa de distância |

## 📝 Validações

O workflow valida:

- ✅ Presença de rua, número e bairro
- ✅ Existência do endereço (via geocoding)
- ✅ Coordenadas válidas
- ✅ Cálculo de distância preciso

## ⚠️ Limitações

- **API Nominatim**: É gratuita mas tem limite de requisições (1 req/segundo). Para uso intenso, considere usar Google Geocoding API.
- **Precisão**: Depende da qualidade dos dados do OpenStreetMap para Montes Claros.
- **Endereços muito novos**: Podem não estar cadastrados no OpenStreetMap.

## 🆙 Melhorias Futuras (Opcional)

Se precisar de mais precisão ou volume:

### Opção 1: Google Geocoding API
- Mais precisa
- Requer API key (tem plano gratuito com limite)
- Melhor cobertura de endereços

### Opção 2: Cache de Endereços
- Salvar endereços já consultados
- Reduz chamadas à API
- Acelera respostas

### Opção 3: Integração com Melhor Envio
- Cotação real de transportadoras
- Múltiplas opções de entrega
- Rastreamento incluso

## 🐛 Resolução de Problemas

### Erro: "Endereço não encontrado"
- Verifique se o endereço existe e está correto
- Tente usar endereço mais específico (nome da rua completo)
- Confirme se o bairro está correto

### Erro: HTTP 429 (Too Many Requests)
- API Nominatim tem limite de 1 requisição/segundo
- Aguarde alguns segundos entre requisições
- Considere implementar cache ou usar Google Geocoding API

### Distância incorreta
- Verifique se as coordenadas de origem estão corretas
- Confira no Google Maps se o local está correto

## 📞 Suporte

Para alterar valores, faixas de distância ou coordenadas:
1. Edite os nodes específicos dentro do workflow n8n
2. Siga as instruções de customização acima
3. Sempre teste após fazer alterações

## 📄 Arquivos do Projeto

```
workflows/
  ├── calcular-frete-endereco.json       # Workflow n8n principal
  └── exemplos-requisicao.json            # Exemplos de teste
README.md                                  # Esta documentação
```

## 🗺️ Como Funciona o Cálculo

1. **Geocoding**: Converte o endereço em coordenadas (lat/lng)
2. **Haversine**: Calcula distância entre dois pontos na esfera terrestre
   - Fórmula: `d = 2r × arcsin(√[sin²((lat2-lat1)/2) + cos(lat1) × cos(lat2) × sin²((lon2-lon1)/2)])`
   - `r` = raio da Terra (6371 km)
3. **Tabela**: Aplica preço baseado na faixa de distância

## 🎓 Exemplo Prático

**Sua loja está em:** Centro de Montes Claros
**Cliente mora em:** Ibituruna

1. Workflow recebe: `Rua X, 100, Ibituruna`
2. Busca coordenadas do endereço: `-16.7234, -43.8234`
3. Calcula distância: `8.5 km`
4. Consulta tabela: `8.5 km` = faixa "8 a 12 km"
5. Retorna: `R$ 25,00`

---

**Desenvolvido para Montes Claros, MG - Brasil** 🇧🇷

**Versão**: 2.0 (Cálculo por Distância/Raio)
