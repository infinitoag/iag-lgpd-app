# IAG LGPD App

Script JavaScript para exibir banner de consentimento de cookies em sites e controlar a execução de tags antes ou depois da decisão do usuário.

O projeto foi criado para facilitar a implementação de avisos de cookies com suporte a Google Tag Manager, Consent Mode V2, Pixel da Meta/Facebook e injeção personalizada de scripts, mantendo uma instalação simples por CDN ou arquivo estático.

## Recursos

- Banner de cookies com quatro formatos disponíveis.
- Controle de tags executadas antes do consentimento.
- Controle de tags executadas somente após o aceite.
- Suporte ao Google Tag Manager.
- Suporte ao Google Consent Mode V2.
- Suporte ao Pixel da Meta/Facebook.
- Gravação do consentimento em cookie por 30 dias.
- Botão opcional para recusa.
- Redirecionamento opcional após recusa.
- Personalização básica de cores e CSS.
- Execução única por carregamento de página.

## Arquivos

| Arquivo | Descrição |
|---|---|
| `iaglpgdapp.js` | Versão legível do script, indicada para desenvolvimento e manutenção. |
| `iaglpgdapp.min.js` | Versão minificada, indicada para uso em produção. |

## Instalação

Adicione o script no `head` do site:

```html
<script src="https://www.infinitoag.com/iag-lgpd-app/iaglpgdapp.min.js"></script>
```

Depois, inicialize o banner com `iagLGPDApp()`:

```html
<script>
  iagLGPDApp({
    gtm: 'GTM-XXXXXXX',
    useConsentModeV2: true,
    policy_url: 'https://www.exemplo.com/politica-de-privacidade/'
  });
</script>
```

## Exemplo completo

```html
<script src="https://www.infinitoag.com/iag-lgpd-app/iaglpgdapp.min.js"></script>

<script>
  iagLGPDApp({
    gtm: 'GTM-XXXXXXX',
    useConsentModeV2: true,
    fb_pixel_id: '000000000000000',
    banner_type: 'bottom_bar',
    message: 'Valorizamos sua privacidade Utilizamos cookies para melhorar sua experiência e personalizar conteúdo. Ao clicar em "Aceitar", você concorda com o uso de todos os cookies.',
    policy_url: 'https://www.exemplo.com/politica-de-privacidade/',
    show_reject_button: true,
    reject_redirect_url: 'https://www.exemplo.com/sem-cookies/',
    banner_bg_color: '#ffffff',
    banner_text_color: '#000000',
    btn_bg_color: '#000000',
    btn_text_color: '#ffffff',
    tags_before: [
      '<script>console.log("Executado antes do consentimento");<\/script>'
    ],
    tags_after: [
      '<script>console.log("Executado após o aceite");<\/script>'
    ],
    custom_css: '.iag-lgpd-banner{border:1px solid #ddd;}'
  });
</script>
```

## Parâmetros

| Parâmetro | Tipo | Padrão | Descrição |
|---|---|---|---|
| `gtm` | `string` | `''` | ID do Google Tag Manager. Exemplo: `GTM-XXXXXXX`. |
| `useConsentModeV2` | `boolean` | `false` | Ativa o Consent Mode V2 quando o GTM estiver configurado. |
| `fb_pixel_id` | `string \| number` | `''` | ID do Pixel da Meta/Facebook. |
| `tags_before` | `string[]` | `[]` | Tags HTML ou JavaScript executadas antes da decisão do usuário. |
| `tags_after` | `string[]` | `[]` | Tags HTML ou JavaScript executadas somente após o aceite. |
| `banner_type` | `string` | `bottom_bar` | Tipo do banner: `bottom_bar`, `floating_left`, `floating_right` ou `modal`. |
| `message` | `string` | Texto padrão | Mensagem exibida no banner. Aceita HTML. |
| `policy_url` | `string` | `''` | URL da política de privacidade. Quando informada, exibe o botão de política. |
| `show_reject_button` | `boolean` | `false` | Exibe o botão de recusa. |
| `reject_redirect_url` | `string` | `''` | URL para redirecionamento após recusa. |
| `banner_bg_color` | `string` | `#FFF` | Cor de fundo do banner. |
| `banner_text_color` | `string` | `#000` | Cor do texto do banner. |
| `btn_bg_color` | `string` | `#000` | Cor de fundo dos botões. |
| `btn_text_color` | `string` | `#FFF` | Cor do texto dos botões. |
| `custom_css` | `string` | `''` | CSS adicional aplicado ao banner. |

## Comportamento

- O banner é exibido apenas quando o cookie `iag_lgpd_consent` não existe.
- Ao aceitar, o script grava o cookie com valor `accepted` por 30 dias.
- Ao recusar, o script grava o cookie com valor `rejected` por 30 dias.
- `tags_before` são executadas no carregamento inicial do script.
- `tags_after` são executadas somente após o aceite.
- Com `useConsentModeV2: true`, o GTM inicia com consentimento negado e atualiza para concedido após o aceite.
- Com `useConsentModeV2: true`, o Pixel da Meta/Facebook é carregado somente após o aceite.
- Com `useConsentModeV2: false`, o Pixel da Meta/Facebook é carregado no início, se `fb_pixel_id` estiver configurado.
- Se `reject_redirect_url` for informado, o usuário é redirecionado após recusar.
- Chamadas repetidas para `iagLGPDApp()` são ignoradas no mesmo carregamento de página.

## Observações técnicas

- As tags informadas em `tags_before` e `tags_after` são inseridas exatamente como recebidas.
- O cookie usa `path=/` e `SameSite=Lax`.
- O CSS padrão inclui responsividade e `z-index` alto para manter o banner visível acima do conteúdo da página.
- O link da política de privacidade abre em nova aba com `rel="noopener noreferrer"`.

## Desenvolvimento

Edite o arquivo legível:

```text
iaglpgdapp.js
```

Depois, atualize a versão minificada:

```text
iaglpgdapp.min.js
```

## Versionamento

Commits sugeridos:

```text
feat: add cookie consent manager
fix: adjust consent mode behavior
docs: improve readme
chore: update minified build
```
