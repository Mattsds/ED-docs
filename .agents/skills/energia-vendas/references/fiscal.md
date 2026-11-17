# Configuração fiscal e emissão

Antes de executar, consulte a documentação atual e a configuração da própria conta. O exemplo existente de recorrência não autoriza enquadrar automaticamente outro serviço.

## Referência encontrada na conta

Nove assinaturas consultadas tinham serviço municipal ID `290628`, nome `31.01 - Serviços técnicos em edificações, eletrônica, eletrotécnica, mecânica, telecomunicações e congêneres.` O ISS era 5%, mas retenções variavam entre clientes, incluindo casos com ISS retido e com PIS/Cofins/CSLL retidos. Esses são dados observados, não uma recomendação tributária nem regra universal.

Reutilize somente uma configuração validada para o serviço e cliente. Não copie campos monetários calculados (`stateIbsValue`, `cbsValue` etc.) de outra venda. Não confunda alíquota normal com retenção. Sem retenção não significa tributos zerados. Preserve configurações tributárias completas e consulte o contrato dos campos enviados: nem todo campo retornado em GET é aceito em POST/PUT.

## Recorrente

Consulte `GET /v3/subscriptions/{id}/invoiceSettings` para obter referência. Configure via `POST /v3/subscriptions/{id}/invoiceSettings` com `effectiveDatePeriod: BEFORE_PAYMENT_DUE_DATE`, `daysBeforeDueDate: 10`, serviço e tributos validados. Assinaturas antigas tinham 15 dias; não as altere ao criar uma nova. Respostas observadas usam também o nome `invoiceCreationPeriod`; não copie esse nome para uma requisição sem conferir a documentação.

## Pontual

A ajuda oficial do painel lista “Quando o seu cliente realizar o pagamento”. Já o endpoint público consultado `POST /v3/invoices` requer `effectiveDate`; não foi encontrado parâmetro documentado equivalente para cobrança pontual. Não invente endpoint `/payments/{id}/invoiceSettings`, nem envie campos desconhecidos supondo que funcionam.

Se houver sessão autenticada no painel e autorização, configure a opção nativa e verifique o resultado visível. Caso contrário, informe a pendência. Uma alternativa é webhook autenticado que, após validar o evento/estado e ausência de nota duplicada, solicita a nota. Essa alternativa exige implementação independente e não está incluída nesta skill. Não configure assinatura artificial para contornar uma venda pontual.

Na consulta inicial, a conta retornou zero webhooks. Consulte novamente antes de concluir que continua assim. Não prometa acompanhamento contínuo com uma skill acionada apenas sob demanda.

## Verificação

Confirme os dados fiscais do tomador, incluindo e-mail e endereço quando exigidos. A nota só está emitida quando autorizada (`AUTHORIZED`/`INVOICE_AUTHORIZED`). Trate erro de emissão sem recriar a cobrança nem duplicar a nota.

## Fontes

- https://docs.asaas.com/reference/criar-novo-cliente
- https://docs.asaas.com/reference/criar-nova-cobranca
- https://docs.asaas.com/reference/criar-nova-assinatura
- https://docs.asaas.com/reference/criar-configuracao-para-emissao-de-notas-fiscais
- https://docs.asaas.com/reference/agendar-nota-fiscal
- https://central.ajuda.asaas.com/hc/pt-br/articles/32088071728667-Como-agendar-a-emiss%C3%A3o-de-nota-fiscal
