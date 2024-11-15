# Workflow-ASL
{
  "Comment": "Workflow de Assistente de Delivery",
  "StartAt": "ReceberPedido",
  "States": {
    "ReceberPedido": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:ReceberPedido",
      "Next": "ValidarPedido",
      "Catch": [
        {
          "ErrorEquals": ["ValidationError"],
          "Next": "ErroRecebimento"
        }
      ]
    },
    "ValidarPedido": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.validacao",
          "StringEquals": "Valido",
          "Next": "SelecionarEntregador"
        }
      ],
      "Default": "ErroValidacaoPedido"
    },
    "SelecionarEntregador": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:SelecionarEntregador",
      "Next": "AtualizarStatus",
      "Catch": [
        {
          "ErrorEquals": ["NoDriversAvailable"],
          "Next": "ErroSelecionarEntregador"
        }
      ]
    },
    "AtualizarStatus": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:AtualizarStatusPedido",
      "Next": "PrepararPedido",
      "Catch": [
        {
          "ErrorEquals": ["NotificationError"],
          "Next": "ErroNotificacao"
        }
      ]
    },
    "PrepararPedido": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:PrepararPedido",
      "Next": "EntregarPedido",
      "Catch": [
        {
          "ErrorEquals": ["PreparationError"],
          "Next": "ErroPreparacaoPedido"
        }
      ]
    },
    "EntregarPedido": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:EntregarPedido",
      "Next": "ConfirmarEntrega",
      "Catch": [
        {
          "ErrorEquals": ["DeliveryError"],
          "Next": "ErroEntrega"
        }
      ]
    },
    "ConfirmarEntrega": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:ConfirmarEntrega",
      "End": true,
      "Catch": [
        {
          "ErrorEquals": ["ConfirmationError"],
          "Next": "ErroConfirmacao"
        }
      ]
    },
    "ErroRecebimento": {
      "Type": "Fail",
      "Error": "PedidoInvalido",
      "Cause": "Erro ao receber informações do pedido."
    },
    "ErroValidacaoPedido": {
      "Type": "Fail",
      "Error": "ValidacaoInvalida",
      "Cause": "Dados do pedido são inválidos."
    },
    "ErroSelecionarEntregador": {
      "Type": "Fail",
      "Error": "EntregadorIndisponivel",
      "Cause": "Nenhum entregador disponível no momento."
    },
    "ErroNotificacao": {
      "Type": "Fail",
      "Error": "FalhaNotificacao",
      "Cause": "Erro ao notificar cliente sobre o status do pedido."
    },
    "ErroPreparacaoPedido": {
      "Type": "Fail",
      "Error": "ErroPreparacao",
      "Cause": "Erro durante a preparação do pedido."
    },
    "ErroEntrega": {
      "Type": "Fail",
      "Error": "ErroEntrega",
      "Cause": "Erro ao entregar o pedido."
    },
    "ErroConfirmacao": {
      "Type": "Fail",
      "Error": "ErroConfirmacao",
      "Cause": "Erro ao confirmar a entrega com o cliente."
    }
  }
}
