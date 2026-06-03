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
- Modo debug opcional via `console.log`.
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
    debug: true,
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
| `debug` | `boolean` | `false` | Ativa logs no console com as etapas do processo e os valores disparados. |

## Comportamento

- O banner é exibido apenas quando o cookie `iag_lgpd_consent` não existe.
- Ao aceitar, o script grava o cookie com valor `accepted` por 30 dias.
- Ao recusar, o script grava o cookie com valor `rejected` por 30 dias.
- Com `useConsentModeV2: true` e `gtm` configurado, o script injeta no `<head>` o estado padrão de consentimento negado antes de abrir o banner.
- Depois do estado padrão de consentimento, o script injeta no `<head>` a Google Tag com o ID informado em `gtm`.
- Depois da Google Tag, `tags_before` são inseridas no `<head>` exatamente como recebidas.
- O banner é exibido após a injeção da Google Tag e de `tags_before`.
- Após o aceite, o script injeta no `<head>` o update de consentimento concedido.
- Depois do update de consentimento, `tags_after` são inseridas no `<head>` exatamente como recebidas.
- Com `useConsentModeV2: true`, o Pixel da Meta/Facebook é carregado somente após o aceite.
- Com `useConsentModeV2: false`, o Pixel da Meta/Facebook é carregado no início, se `fb_pixel_id` estiver configurado.
- Se `reject_redirect_url` for informado, o usuário é redirecionado após recusar.
- Chamadas repetidas para `iagLGPDApp()` são ignoradas no mesmo carregamento de página.

## Ordem de injeção no head

Quando `gtm` e `useConsentModeV2: true` estiverem configurados, a ordem de injeção é:

1. Estado padrão de consentimento negado.
2. Google Tag padrão com o ID informado em `gtm`.
3. Tags configuradas em `tags_before`.
4. Banner/modal de consentimento.
5. Após o aceite, update de consentimento concedido.
6. Tags configuradas em `tags_after`.

O trecho inicial inserido antes do banner segue esta estrutura:

```html
<!-- 1. Definir o estado de consentimento padrão -->
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}

  // No Modo Avançado, definimos como 'denied' por padrão para regiões como o EEE/Brasil
  gtag('consent', 'default', {
    'ad_storage': 'denied',              // Bloqueia cookies de anúncios
    'ad_user_data': 'denied',            // Bloqueia envio de dados de usuário
    'ad_personalization': 'denied',      // Bloqueia remarketing
    'analytics_storage': 'denied',       // Bloqueia cookies de estatísticas
    'wait_for_update': 500               // (Opcional) Tempo para esperar o banner carregar
  });
</script>

<!-- 2. Carregar a Google Tag (Snippet padrão) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=TAG_ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'TAG_ID'); // Aqui a tag dispara o "ping" anônimo porque o padrão é 'denied'
</script>
```

Após o aceite, este trecho é inserido no `<head>`:

```html
<!-- 3. Este comando avisa ao Google que o usuário deu permissão -->
<script>
gtag('consent', 'update', {
  'ad_storage': 'granted',
  'ad_user_data': 'granted',
  'ad_personalization': 'granted',
  'analytics_storage': 'granted'
});
</script>
```

## Debug

Para debugar o fluxo, inicialize com `debug: true`:

```html
<script>
  iagLGPDApp({
    debug: true,
    gtm: 'GTM-XXXXXXX',
    useConsentModeV2: true,
    tags_before: [
      '<script>console.log("Tag before");<\/script>'
    ],
    tags_after: [
      '<script>console.log("Tag after");<\/script>'
    ]
  });
</script>
```

Com o debug ativo, o script registra no console:

- início da execução;
- cookie atual de consentimento;
- `tags_before` disparadas;
- `tags_after` disparadas após o aceite;
- Google Tag carregada;
- Pixel da Meta/Facebook carregado;
- aceite ou recusa;
- cookie gravado;
- payload enviado ao Consent Mode V2.

No update do Consent Mode V2, o log inclui o objeto `consentData` usado no trecho injetado no `<head>`.

## Observações técnicas

- As tags informadas em `tags_before` e `tags_after` são inseridas exatamente como recebidas no `<head>`.
- Os comentários dos snippets do Google são criados como nós reais de comentário no DOM.
- Como a injeção é feita por JavaScript em runtime, valide o resultado em DevTools > Elements > `head`. A opção "Exibir código-fonte da página" mostra apenas o HTML original recebido do servidor.
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
