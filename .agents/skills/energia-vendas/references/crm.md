# CRM Energia Digital

Projeto Supabase identificado: `energiadigital`, referência `ceeshmunbiekfkdjfhxm`. Mapeamento consultado em setembro de 2026; confira mudanças antes de operar em outro ambiente.

## Cliente — public.crm_clients

`nome` é obrigatório. Campos disponíveis: `documento`, `email`, `telefone`, `empresa`, `contato_nome`, `contato_cargo`, `notas`, `app_user_id`, `is_pagante`. IDs, código e timestamps são gerados pelo banco. Não invente `app_user_id` nem marque pagamento confirmado pela simples criação da cobrança. Endereço fiscal não tem colunas próprias nesta tabela; registre-o nas notas, se necessário, preservando conteúdo anterior.

## Card — public.crm_deals

Obrigatórios sem default: `client_id`, `titulo`, `service_type`. Informe `stage: onboarding` explicitamente.

Serviços: `ressarcimento`, `titularidade`, `monitoramento`, `plataforma`, `gestao_usina`, `outro`. Visita técnica avulsa foi mapeada para `outro`, com descrição explícita no título.

Etapas: `lead`, `proposta`, `contrato`, `onboarding`, `em_andamento`, `concluido`, `perdido`.

Valores: `valor`, `valor_mensal`, `valor_entrada`, `percentual_exito`, `valor_realizado`, `receita_tipo` (pontual/recorrente). Não transforme percentual de êxito em cobrança sem base de cálculo e valor devido. Não suponha a semântica dos valores dos planos sem consultar a venda.

Contrato/plano: `monitoring_plan` (essencial/completo/personalizado), `contract_start_date`, `contract_months`, `plano_detalhes`. Outros: `procuracao_status`, `numero_placas`, `valor_plano_1`, `valor_plano_2`, campos de localização e `faturamento_obs`. Não há campo de responsável no card na estrutura consultada.

Um trigger força `receita_tipo=recorrente` para monitoramento, plataforma e gestão de usina. Se o pedido contradisser essa regra, esclareça o mapeamento sem alterar o banco ou o serviço silenciosamente.

Mudar a etapa para `em_andamento` por UPDATE ativa o contrato e pode gerar tarefas de monitoramento. Inserir diretamente nessa etapa não dispara os mesmos gatilhos. Não avance a etapa por ter criado uma cobrança.

`public.crm_activities` permite histórico por `client_id` e `deal_id`, com `tipo` e `conteudo`. Não invente autor autenticado.

Não foram identificados campos estruturados de Asaas no CRM. Registre IDs e link em `faturamento_obs` e use referências no Asaas. Não crie schema, webhook ou função como parte implícita de uma venda.
