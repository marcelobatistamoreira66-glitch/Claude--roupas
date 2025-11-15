# Workflows para Enviar Imagem via Mega API

## Problema Resolvido

O erro de imagem "pending" ocorre quando a Mega API não consegue baixar a imagem da URL externa. Isso pode acontecer por:
- Timeout na requisição
- URL bloqueada ou inacessível
- Problemas de redirect
- Formato de imagem incompatível

## Soluções Disponíveis

### 🚀 RECOMENDADO: Upload Direto (Base64)

**Arquivo:** `enviar-imagem-mega-api-upload-direto.json`

**Como funciona:**
1. ✅ Baixa a imagem do HTTP
2. ✅ Converte para Base64
3. ✅ Envia diretamente para Mega API com os dados da imagem

**Vantagens:**
- ✅ Não depende de URLs externas acessíveis
- ✅ Mais confiável
- ✅ Resolve o problema de "pending"
- ✅ Funciona com qualquer fonte de imagem

**Endpoint usado:** `/media` (com mediaBase64)

**Como usar:**
```json
// No nó "Configurar Dados", defina:
{
  "imageUrl": "https://sua-url-da-imagem.com/imagem.jpg",
  "destino": "5511999999999",  // Telefone com DDI
  "legenda": "Texto da legenda (opcional)"
}
```

**Estrutura do workflow:**
```
Inicio → Configurar Dados → Baixar Imagem HTTP → Converter Base64 → Enviar Mega API → Formatar Resposta
```

---

### 🔄 Alternativa: Envio por URL

**Arquivo:** `enviar-imagem-mega-api-simples.json`

**Como funciona:**
1. Define as variáveis
2. Baixa a imagem (para validar)
3. Envia a URL original para a Mega API

**Vantagens:**
- Mais rápido (não converte base64)
- Menos processamento

**Desvantagens:**
- ⚠️ Pode dar "pending" se a URL não for acessível pela Mega API
- ⚠️ Depende de URLs públicas e acessíveis

**Endpoint usado:** `/mediaUrl` (com url)

---

### 🔀 Solução Inteligente: Workflow Híbrido

**Arquivo:** `enviar-imagem-mega-api-hibrido.json`

**Como funciona:**
1. 🎯 Tenta primeiro enviar via URL (mais rápido)
2. ✅ Se funcionar, retorna sucesso
3. 🔄 Se der "pending" ou erro, automaticamente baixa a imagem
4. ✅ Converte para base64 e envia novamente

**Vantagens:**
- ✅ Melhor dos dois mundos
- ✅ Otimiza quando URL funciona
- ✅ Fallback automático para base64
- ✅ Não precisa decidir qual usar

**Estrutura do workflow:**
```
Inicio → Configuracao → Tentar Envio via URL → URL Funcionou?
                                                    ↓ SIM → Sucesso via URL
                                                    ↓ NÃO → Download Imagem → Para Base64 → Enviar Base64 → Sucesso via Base64
```

**Quando usar:**
- ✅ Quando não sabe se a URL será acessível pela Mega API
- ✅ Para ter maior taxa de sucesso
- ✅ Em ambientes de produção

---

## Configuração da Mega API

**Credenciais necessárias:**
- Instance: `megastart-M37aHjihk11`
- Token: `M37aHjihk11`
- Base URL: `https://apistart02.megaapi.com.br`

**Endpoints disponíveis:**

1. **`/rest/sendMessage/{instance}/mediaUrl`**
   - Envia imagem por URL externa
   - Mega API baixa a imagem
   - Pode dar timeout/pending

2. **`/rest/sendMessage/{instance}/media`** ⭐ RECOMENDADO
   - Envia imagem em base64
   - Mais confiável
   - Sem dependência de URLs externas

---

## Formato do JSON para Mega API

### Envio por URL:
```json
{
  "messageData": {
    "to": "5511999999999",
    "url": "https://exemplo.com/imagem.jpg",
    "fileName": "imagem.jpg",
    "type": "image",
    "caption": "Legenda opcional",
    "mimeType": "image/jpeg"
  }
}
```

### Envio por Base64:
```json
{
  "messageData": {
    "to": "5511999999999",
    "mediaBase64": "iVBORw0KGgoAAAANSUhEUgAA...",
    "fileName": "imagem.jpg",
    "type": "image",
    "caption": "Legenda opcional",
    "mimeType": "image/jpeg"
  }
}
```

---

## Tipos MIME Suportados

- `image/jpeg` - JPEG/JPG
- `image/png` - PNG
- `image/gif` - GIF
- `image/webp` - WebP

---

## Troubleshooting

### ❌ Erro: "pending"
**Causa:** Mega API não conseguiu baixar a imagem da URL

**Solução:** Use o workflow `enviar-imagem-mega-api-upload-direto.json`

---

### ❌ Erro: "timeout"
**Causa:** Imagem muito grande ou conexão lenta

**Solução:**
1. Aumente o timeout no nó HTTP Request
2. Otimize/comprima a imagem antes

---

### ❌ Erro: "invalid base64"
**Causa:** Dados corrompidos na conversão

**Solução:** Verifique o nó de conversão base64

---

## Exemplo de Uso Completo

### Cenário: Enviar imagem do Cloudinary via WhatsApp

1. Importe o workflow `enviar-imagem-mega-api-upload-direto.json`

2. Configure o nó "Configurar Dados":
```json
{
  "imageUrl": "https://res.cloudinary.com/dqiextr5u/image/upload/v1763179608/Imagem_do_WhatsApp_de_2025-11-13_%C3%A0_s_20.11.43_412193f7_buypot.jpg",
  "destino": "5511987654321",
  "legenda": "Confira nosso produto!"
}
```

3. Execute o workflow

4. Resultado esperado:
```json
{
  "status": "Imagem enviada com sucesso para 5511987654321",
  "resposta": {
    "success": true,
    "messageId": "..."
  }
}
```

---

## Integração com outros workflows

Você pode integrar este workflow com:
- ✅ Workflow de atendimento (já existente no projeto)
- ✅ Workflow de pedidos
- ✅ Workflow de up-sell de bebidas
- ✅ Qualquer webhook que receba URLs de imagens

**Exemplo de integração:**
```json
// No workflow principal, após receber a URL da imagem:
{
  "imageUrl": "{{ $json.product_image }}",
  "destino": "{{ $json.customer_phone }}",
  "legenda": "{{ $json.product_description }}"
}
```

---

## Observações Importantes

1. ⚠️ **Tamanho máximo:** Verifique os limites da Mega API para base64
2. ⚠️ **Rate limiting:** Respeite os limites de requisições por minuto
3. ⚠️ **Formato do telefone:** Sempre com DDI (55 para Brasil)
4. ✅ **Timeout:** Configurado para 120 segundos (upload direto)

---

## Suporte

- Documentação Mega API: https://megaapi.com.br/docs
- Issues do projeto: GitHub
- Logs do n8n: Verifique execuções com erro

---

## Changelog

**v1.0 - 2025-11-15**
- ✅ Criado workflow de upload direto (base64) - RECOMENDADO
- ✅ Criado workflow simplificado (URL)
- ✅ Criado workflow híbrido (URL + fallback Base64) - PRODUÇÃO
- ✅ Adicionado exemplos de teste e validação
- ✅ Adicionado tratamento de erros
- ✅ Documentação completa

## Resumo de Qual Workflow Usar

| Cenário | Workflow Recomendado | Por quê? |
|---------|---------------------|----------|
| URL externa confiável e rápida | `enviar-imagem-mega-api-simples.json` | Mais rápido, menos processamento |
| URL pode dar problema | `enviar-imagem-mega-api-upload-direto.json` | Garante entrega, resolve "pending" |
| Produção (máxima confiabilidade) | `enviar-imagem-mega-api-hibrido.json` | Tenta URL primeiro, fallback automático |
| Cloudinary ou CDN lento | `enviar-imagem-mega-api-upload-direto.json` | Não depende de acesso externo da Mega API |
