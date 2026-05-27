# iag-lgpd-app

## Sobre o projeto

git remote add origin https://github.com/infinitoag/iag-lgpd-app.git

Ele: 
- exibe banner de cookies em diferentes formatos
- injeta tags pré-carregadas (`tags_before`) imediatamente
- injeta tags somente após aceitação (`tags_after`)
- suporta Google Tag Manager com Consent Mode V2
- grava consentimento em cookie por 30 dias
- permite recusa com bloqueio de tags opcionais e redirecionamento
- permite injeção de CSS personalizado no banner

## Arquivos

- `iaglpgdapp.js` — versão legível do script
- `iaglpgdapp.min.js` — versão minificada pronta para produção

## Como usar

Adicione o script no `head` do seu site:

```html
<script src="https://www.infinitoag.com/iag-lgpd-app/iaglpgdapp.min.js"></script>
```

Depois, inicialize com `iagLGPDApp(args)`:

```html
<script>
  iagLGPDApp({
    gtm: 'GTM-PP4FJKV',
    banner_type: 'bottom_bar',
    policy_url: 'https://usitom.com.br/politica-de-privacidade/',
    show_reject_button: true,
    reject_redirect_url: 'https://usitom.com.br/sem-cookies',
    banner_bg_color: '#ffffff',
    banner_text_color: '#000000',
    btn_bg_color: '#000000',
    btn_text_color: '#ffffff',
    tags_before: [
      '<script>console.log("tag before")</script>'
    ],
    tags_after: [
      '<script>console.log("tag after")</script>'
    ],
    custom_css: '.iag-lgpd-banner{border:1px solid #ddd;}'
  });
</script>
```

## Parâmetros disponíveis

| Parâmetro | Tipo | Padrão | Descrição |
|---|---|---|---|
| `gtm` | `string` | `''` | Código do Google Tag Manager (ex: `GTM-XXXXXXX`). | 
| `useConsentModeV2` | `boolean` | `false` | Ativa o Consent Mode V2 quando `gtm` está definido. | 
| `tags_before` | `string[]` | `[]` | Tags HTML/JS que serão inseridas imediatamente, antes da decisão do usuário. |
| `tags_after` | `string[]` | `[]` | Tags HTML/JS que serão inseridas somente após o usuário aceitar. |
| `banner_type` | `string` | `bottom_bar` | Formato do banner: `bottom_bar`, `floating_left`, `floating_right`, `modal`. |
| `message` | `string` | Texto padrão | Texto de aviso do banner. Suporta HTML. |
| `policy_url` | `string` | `''` | URL da política de privacidade; se informado, exibe o botão. |
| `show_reject_button` | `boolean` | `false` | Exibe botão de recusar. |
| `reject_redirect_url` | `string` | `''` | URL para redirecionar após recusa. |
| `banner_bg_color` | `string` | `#FFF` | Cor de fundo do banner. |
| `banner_text_color` | `string` | `#000` | Cor do texto do banner. |
| `btn_bg_color` | `string` | `#000` | Cor de fundo dos botões. |
| `btn_text_color` | `string` | `#FFF` | Cor do texto dos botões. |
| `custom_css` | `string` | `''` | CSS adicional para personalizar o banner. |

## Comportamento

- O banner só é exibido se o cookie `iag_lgpd_consent` não existir.
- Após aceitar, o cookie é gravado por 30 dias e `tags_after` são injetadas.
- Após recusar, o cookie também é gravado por 30 dias e `tags_after` não são injetadas.
- Caso `reject_redirect_url` seja informado, o usuário é redirecionado após recusa.
- Chamadas repetidas a `iagLGPDApp(args)` são ignoradas; o script roda apenas uma vez.

## Observações

- `tags_before` são inseridas exatamente como fornecidas.
- `gtm` com `useConsentModeV2: true` injeta GTM e define consentimento inicial como negado. Após aceitação, envia `gtag('consent','update',...)` com valores `granted`.
- O CSS padrão garante responsividade e `z-index` alto para aparecer acima do conteúdo.

## Repositório Git

Use este repositório para versionar o arquivo principal e a versão minificada.

### Exemplo de commit

- `feat: add iag-lgpd-app cookie consent manager`
- `chore: add minified build iaglpgdapp.min.js`
- `docs: add README with usage and parameters`
