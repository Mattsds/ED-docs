---
name: energia-vendas
description: Gerar contratos da Energia Digital pelo gerador HTML local, preparar assinatura no ZapSign, cadastrar cliente e venda no CRM e criar cobranças e notas no Asaas com coleta dos dados faltantes.
---

# Energia Digital — vendas e faturamento

## Acesso e escopo

Esta skill contém instruções; não fornece ferramentas, credenciais nem um processo contínuo de webhook. O agente precisa de acesso autorizado ao Supabase e à API do Asaas. Obtenha a chave do gerenciador de segredos do ambiente como `ASAAS_API_KEY`; nunca a inclua no repositório, em argumentos de comandos, logs ou respostas. Confirme a conta e o ambiente. Sandbox e produção têm credenciais e cadastros separados. Uma operação de teste em produção é real.

Execute somente as operações solicitadas: cliente, card, cobrança e/ou configuração fiscal. A criação isolada de card não autoriza cobrança. Um pedido completo já autoriza as etapas correspondentes; não solicite aprovação repetida. Não transfira dinheiro, cancele cobranças, altere assinaturas anteriores ou envie mensagens adicionais sem solicitação.

Leia [o mapeamento do CRM](references/crm.md) antes de gravar dados e [as regras fiscais](references/fiscal.md) antes de configurar notas.

## Coleta de dados

Para contrato, leia [contratos e ZapSign](references/contratos.md). O fluxo aprovado é gerar contrato e cobrança juntos, sem aguardar assinatura, e manter o card em onboarding. A implantação única fica somente como nota/observação do card, nunca nos campos de valor do CRM; sua cobrança no Asaas continua separada quando aplicável.

Consulte cadastros antes de perguntar. Normalize CPF/CNPJ para comparar; nome sozinho não é identificação suficiente quando houver ambiguidade. Reutilize registros existentes, sem sobrescrever divergências silenciosamente.

Reúna as perguntas faltantes em um bloco, adaptado ao serviço:

- Cliente novo: nome/razão social e CPF/CNPJ para o fluxo com Asaas. Contato e endereço fiscal conforme exigidos para nota e notificações.
- Contrato PJ: representante com nome, CPF, cargo e e-mail de assinatura; contato e telefone com DDD; e-mail financeiro. Diferencie e-mail financeiro de login na plataforma. Reutilize dados existentes informando sua origem; pergunte divergências.
- PF: nome, CPF, e-mail, telefone e endereço completo com CEP (não há CNPJ para buscar endereço).
- Usinas: pergunte quantas. Para cada uma: código SolarView, AC (kW), DC (kWp), número da UC onde está ligada e endereço dessa UC. Não suponha que o endereço da UC é o endereço fiscal.
- Venda: serviço, descrição e valor. Identifique se é pontual ou recorrente; monitoramento requer plano quando relevante.
- Pontual: vencimento. Não há prazo padrão aprovado.
- Recorrente: mensal e por prazo indeterminado. Não pergunte duração da assinatura. Vigência do contrato no CRM é um dado separado; não invente 12 meses.
- Retenção, em ambos os tipos: pergunte se haverá retenção, oferecendo sem retenção como padrão. Resposta ausente não confirma ausência de retenção. Se houver, obtenha tributos e alíquotas. Quando já existir configuração para o mesmo cliente/serviço, apresente-a para validar sua continuidade.

Dados públicos de CNPJ podem ser pesquisados quando autorizado. Identifique a fonte e diferenças entre fontes; não apresente um agregador como consulta direta à Receita. Não use endereço da usina como endereço fiscal sem fundamento.

## Padrões aprovados

- Novo card: `onboarding`.
- Pagamento: `billingType: UNDEFINED`, permitindo escolher entre os meios disponíveis na conta.
- Assinatura: `cycle: MONTHLY`, sem `endDate` nem `maxPayments`.
- Use a data local em `America/Sao_Paulo`. Criação até dia 10 inclusive: primeiro vencimento dia 20 do mesmo mês. Do dia 11 em diante: dia 20 do mês seguinte. Próximos vencimentos dia 20.
- NFS-e recorrente: 10 dias antes do vencimento, inclusive solicitação no próprio dia 10 quando criada nessa data. Confira o processamento efetivo no Asaas.
- NFS-e pontual: somente após pagamento; não substitua por emissão imediata ou no vencimento sem instrução do usuário.
- Exceção explicitamente solicitada vale para a venda correspondente, não altera automaticamente o padrão global.

## Execução e retomada

1. Valide dados, autorização e acesso; apresente as datas absolutas interpretadas de expressões como “segunda que vem”.
2. Localize/crie cliente no CRM e depois card vinculado. Se criar ambos, prefira uma transação. Gere título com cliente e serviço. Não copie dados de uma venda de exemplo.
3. Localize/crie cliente no Asaas. Use o ID do CRM em `externalReference` e preserve o ID retornado.
4. Antes de criar cobrança, confira operações existentes para o card. Use `crm-deal:<id>` como referência; para múltiplas cobranças legítimas do mesmo card, diferencie a operação. `externalReference` é vínculo de busca, não garantia de idempotência.
5. Pontual: `POST /v3/payments`, com cliente, valor, descrição, vencimento e forma aberta. Recorrente: `POST /v3/subscriptions`, aplicando os padrões acima.
6. Registre imediatamente IDs e link da resposta em `faturamento_obs`, preservando conteúdo existente. Verifique o estado retornado. Configure a nota apenas se os dados fiscais e a capacidade necessária estiverem disponíveis.
7. Em timeout ou resultado incerto, consulte pelo cliente/referência antes de repetir. Se houver mais de um candidato, pare e esclareça. Se uma etapa falhar, preserve as bem-sucedidas e retome só a pendência. Não apague registros como compensação automática.
8. Entregue cliente/card criados, valor, vencimento, link e status da cobrança; diferencie nota pendente de configuração, agendada, processando, autorizada e com erro. Cobrança criada não significa pagamento recebido.

## Limites da versão

ZapSign e geração por HTML são operações acionadas sob demanda. Webhook não desperta o chat; não existe acompanhamento contínuo implementado. Não confunda contrato de 12 meses renovável com término da assinatura: a cobrança recorrente permanece por prazo indeterminado. Condições específicas da proposta aprovada prevalecem sobre os padrões acima, incluindo nota no dia 5 e vencimento dia 20.

O fluxo manual cliente → card → cobrança pontual foi executado com sucesso nesta conta. Esta skill não foi validada como agente autônomo de ponta a ponta. A emissão pontual após pagamento ainda requer configuração no painel ou uma integração de eventos implementada e testada. Não afirme que ela está automática apenas por registrar uma observação no CRM.
