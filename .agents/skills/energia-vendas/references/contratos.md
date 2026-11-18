# Contratos, pastas e ZapSign

## A proposta é o contrato

**Não existe gerador de contrato separado.** O documento que o cliente assina é a própria proposta gerada pelo `gerador-de-propostas.html`: ela traz as condições comerciais e as cláusulas. Neste fluxo, "contrato" significa a proposta aprovada, formalizada e assinada. Não procure nem invente um modelo de contrato à parte.

## Fontes locais permanentes

- Gerador: `C:\Users\Viviane\OneDrive\Trabalho\ED\03_Comercial\Gera-Proposta\gerador-de-propostas.html`. O arquivo não está versionado no Git: a cópia no OneDrive é a única. Preserve o original e não o edite para gerar uma venda.
- Registro de propostas: planilha Google `1H5YaWeIYNtEJsJDyovwSB-H4wVCl2WXDdeutX9DIHqo`, aba `Propostas` (colunas: Registrado em, Proposta, Cliente, CPF/CNPJ, Telefone, E-mail, CEP cobrança, Zona, Usinas, Painéis, Detalhe das usinas, Plano escolhido, Essencial, Completo, Implantação, Desconto, Ajuste, Tabela, Arquivo de origem, Observação). Busque a proposta aqui pelo número ou cliente antes de perguntar valores.
- Propostas emitidas: ficam soltas em `ED\03_Comercial\Leads\` enquanto o cliente não fecha, e em `Leads\_Perdidos\<código - nome>\` quando é perdido. Ao fechar, a proposta vai para a pasta do cliente.
- Números de proposta se repetem no histórico (o gerador reiniciava o sequencial por navegador). Identifique a proposta por **número + cliente**, nunca pelo número sozinho.

## Pastas de cliente

Raiz: `C:\Users\Viviane\OneDrive\Trabalho\ED\04_Projetos\Em-andamento\NNN - Nome`.

`NNN` é o **código do cliente no CRM** (`crm_clients.codigo`, 3 dígitos) — não o próximo número livre da pasta. Os números têm buracos de propósito e nunca são renumerados: o código está em contrato assinado e no CRM.

Cliente novo: copie `04_Projetos\Em-andamento\_MODELO - copie e renomeie`, renomeie para `NNN - Nome` e apague as pastas dos serviços que ele não contratou (vêm vazias). Nunca trabalhe dentro do `_MODELO`.

```
NNN - Nome\
├─ Contas\                      ← na RAIZ do cliente, serve a todos os serviços
├─ Monitoramento\               ← só os serviços contratados
│   ├─ Contrato\
│   ├─ Relatórios\
│   └─ Vistorias\               ← só no monitoramento
├─ Auditoria\                   ← Contrato\, Relatórios\
└─ Consultoria\                 ← Contrato\, Relatórios\
```

Use esses nomes exatos, no plural como estão acima. Conta de energia nunca vai dentro do serviço: vai em `Contas\`. Usina é o código do cliente mais uma letra (`027A`, `027B`), dentro do serviço, seguindo a letra que a plataforma já usa em `premium_plants.codigo_usina`.

A proposta assinada vai em `<serviço>\Contrato`. Salve PDF e HTML preenchido lá. Não sobrescreva arquivo existente; versão nova é `_v02`, `_v03`, sem "Copia", "(1)" ou "final".

Modelo em branco (procuração, orçamento, planilha de concessionária) fica em `ED\05_Procedimentos-e-Modelos\Modelos\`. **Copie para a pasta do cliente antes de preencher** — nunca preencha o arquivo que está em `Modelos\`.

Caminhos são configuração deste computador; no Hermes/WSL confirme o mapeamento local.

## Proposta aprovada → documento assinado

Receba a proposta aprovada e dados complementares; extraia valores, plano, painéis, implantação, vigência, vencimentos e regra fiscal. PDF sem texto exige leitura visual. Não recalcule condições aprovadas com a tabela atual do gerador. Não use valores nem dados pessoais de clientes anteriores.

O gerador possui um template HTML em `<script type="text/plain" id="tpl">` e placeholders `{{campo}}`. É possível preencher esse template mantendo layout e cláusulas — é assim que a proposta vira o documento a assinar. O botão gerar também registra na planilha externa e consome um número do sequencial: não dispare esse efeito sem autorização, e não gere número novo para formalizar uma proposta que já tem o seu. Inclua representante, CPF, cargo, e-mail e endereço quando não houver campo nativo. Preserve cláusulas e condições, sem inventar assinantes, testemunhas ou uma revisão jurídica.

Renderize PDF em A4 com fundos e fontes carregadas; confira todas as páginas, textos cortados, dados e ausência de placeholders. Preserve número da proposta aprovada; use data real da formalização. Não crie afirmação de assinatura ou aceite inexistente.

## CRM e cobrança

Reutilize cliente por CPF/CNPJ e card da mesma venda. Card em onboarding mesmo enquanto aguarda assinatura. Implantação: registrar somente em faturamento_obs/nota do card, nunca valor_entrada, valor ou valor_mensal. A constraint crm_deals_valor_por_tipo proíbe valor_entrada em venda recorrente. Mensalidade vai em valor_mensal. Não criar card adicional de implantação sem necessidade expressa.

Crie contrato e cobranças no fluxo autorizado sem esperar assinatura. Cobrança da implantação é pontual; assinatura é mensal indefinida. Evite duplicatas por referências distintas: crm-deal:<id>:mensalidade e :implantacao. Nota fiscal depende da condição acordada; contrato/cobrança criados não autorizam emitir imediatamente quando a regra é futura.

## ZapSign

Credencial em `ZAPSIGN_API_TOKEN` via ambiente/gerenciador de segredos, nunca no Git ou skill. Antes de criar, procure documento existente por external_id e nome; listagem é paginada. POST https://api.zapsign.com.br/api/v1/docs/ com Authorization Bearer, name, base64_pdf, lang pt-br, external_id crm-deal:<id>, folder_path e signers. Inclua somente signatários definidos. Gere o link sem envio automático por padrão: disable_signer_emails=true, send_automatic_email=false e send_automatic_whatsapp=false. Envio de convite exige solicitação explícita. Não assine pelo usuário.

Registre token do documento, links de assinatura e status no card e em arquivo privado da pasta do cliente. Não grave links privados, CPF, contratos ou credenciais no repositório da skill. Confirme status pela API quando solicitado. Guarde arquivo assinado localmente quando disponível; original_file e signed_file podem ser temporários.

Documentação: https://docs.zapsign.com.br/documentos/criar-documento e https://docs.zapsign.com.br/documentos/detalhar-documento. Consulte esquema atual antes de usar campos novos. Uma skill não instala serviço, webhook ou integração contínua.
