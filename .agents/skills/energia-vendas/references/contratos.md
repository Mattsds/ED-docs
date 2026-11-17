# Contratos, pastas e ZapSign

## Fontes locais permanentes

- Gerador: `C:\Users\Viviane\OneDrive\Trabalho\ED\03_Comercial\Gera-Proposta\gerador-de-propostas.html` (versionado em github.com/Mattsds/ED-docs).
- Registro de propostas: planilha Google `1H5YaWeIYNtEJsJDyovwSB-H4wVCl2WXDdeutX9DIHqo`, aba `Propostas` (colunas: Registrado em, Proposta, Cliente, CPF/CNPJ, Telefone, E-mail, CEP cobrança, Zona, Usinas, Painéis, Detalhe das usinas, Plano escolhido, Essencial, Completo, Implantação, Desconto, Ajuste, Tabela, Arquivo de origem, Observação). Busque a proposta aqui pelo número ou cliente antes de perguntar valores.
- Pastas de cliente: `C:\Users\Viviane\OneDrive\Trabalho\ED\04_Projetos\Em-andamento\NNN - Nome` (próximo número livre; subpastas existentes: Contas, Monitoramento, Auditoria). Contrato vai em subpasta `Contrato`, criada se não existir. Não sobrescreva arquivos.

Leia a versão atual do gerador a cada venda. Preserve o arquivo original. Copie a estrutura do modelo somente se o destino não existir; não sobrescreva contratos existentes. Salve PDF e HTML preenchido em Contrato. Caminhos são configuração deste computador; no Hermes/WSL confirme o mapeamento local.

## Proposta aprovada → contrato

Receba a proposta aprovada e dados complementares; extraia valores, plano, painéis, implantação, vigência, vencimentos e regra fiscal. PDF sem texto exige leitura visual. Não recalcule condições aprovadas com a tabela atual do gerador. Não use valores nem dados pessoais de clientes anteriores.

O gerador inspecionado possui um template HTML em `<script type="text/plain" id="tpl">` e placeholders `{{campo}}`. É possível preencher esse template mantendo layout e cláusulas. O botão gerar também registra em planilha externa: não dispare esse efeito sem autorização. Inclua representante, CPF, cargo, e-mail e endereço no contrato derivado quando não houver campo nativo. Preserve cláusulas e condições, sem inventar assinantes, testemunhas ou uma revisão jurídica.

Renderize PDF em A4 com fundos e fontes carregadas; confira todas as páginas, textos cortados, dados e ausência de placeholders. Preserve número da proposta aprovada; use data real da formalização. Não crie afirmação de assinatura ou aceite inexistente.

## CRM e cobrança

Reutilize cliente por CPF/CNPJ e card da mesma venda. Card em onboarding mesmo enquanto aguarda assinatura. Implantação: registrar somente em faturamento_obs/nota do card, nunca valor_entrada, valor ou valor_mensal. A constraint crm_deals_valor_por_tipo proíbe valor_entrada em venda recorrente. Mensalidade vai em valor_mensal. Não criar card adicional de implantação sem necessidade expressa.

Crie contrato e cobranças no fluxo autorizado sem esperar assinatura. Cobrança da implantação é pontual; assinatura é mensal indefinida. Evite duplicatas por referências distintas: crm-deal:<id>:mensalidade e :implantacao. Nota fiscal depende da condição acordada; contrato/cobrança criados não autorizam emitir imediatamente quando a regra é futura.

## ZapSign

Credencial em `ZAPSIGN_API_TOKEN` via ambiente/gerenciador de segredos, nunca no Git ou skill. Antes de criar, procure documento existente por external_id e nome; listagem é paginada. POST https://api.zapsign.com.br/api/v1/docs/ com Authorization Bearer, name, base64_pdf, lang pt-br, external_id crm-deal:<id>, folder_path e signers. Inclua somente signatários definidos. Gere o link sem envio automático por padrão: disable_signer_emails=true, send_automatic_email=false e send_automatic_whatsapp=false. Envio de convite exige solicitação explícita. Não assine pelo usuário.

Registre token do documento, links de assinatura e status no card e em arquivo privado da pasta do cliente. Não grave links privados, CPF, contratos ou credenciais no repositório da skill. Confirme status pela API quando solicitado. Guarde arquivo assinado localmente quando disponível; original_file e signed_file podem ser temporários.

Documentação: https://docs.zapsign.com.br/documentos/criar-documento e https://docs.zapsign.com.br/documentos/detalhar-documento. Consulte esquema atual antes de usar campos novos. Uma skill não instala serviço, webhook ou integração contínua.
