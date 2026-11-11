# Instalação e configuração

[← Voltar ao README](../README.md)

## 1. Requisitos

- Android com chip SIM ativo e acesso à rede celular.
- Wi-Fi estável e conectividade entre Android e central SIP.
- Shizuku e Voice SIP Agent instalados a partir de fontes oficiais.
- Issabel/Asterisk configurado com **PJSIP**.
- Softphone SIP (por exemplo, MicroSIP).
- Acesso administrativo autorizado à central.

**Exemplo de topologia (IPs reservados para documentação):**

| Papel | Endereço de exemplo |
| --- | --- |
| Issabel/Asterisk | `192.0.2.10` |
| Android + Voice SIP Agent | `192.0.2.71` |
| MicroSIP | `192.0.2.85` |
| Endpoint do Android | `6000` |
| Ramal do MicroSIP | `1001` |

> A rede `192.0.2.0/24` não deve ser usada como configuração real de sua LAN. Substitua por IPs privados acessíveis.

## 2. Preparar Shizuku e permissões Android

1. Instale o **Shizuku** e siga suas instruções para iniciar o serviço por **Wireless debugging**, quando suportado.
2. Autorize o **Voice SIP Agent** nas permissões do Shizuku.
3. Verifique os indicadores na seção **Health** do Voice SIP Agent.
4. Autorize permissões de telefone/chamadas solicitadas pelo Android.
5. Nas configurações do sistema, habilite a execução de chamadas e a atividade em segundo plano do aplicativo; revise **Battery optimization**, **Background activity** e opções equivalentes conforme o fabricante.
6. Caso o aparelho limite apps em segundo plano, faça um teste com a tela bloqueada.

**Em alguns Realme/ColorOS:** a opção de desenvolvedor **Disable permission monitoring** pode ajudar quando o Shizuku acusa restrição de ADB. Essa mudança reduz uma proteção do Android; avalie os riscos e use apenas no ambiente de teste.

**Atenção:** após reiniciar o celular, o Shizuku iniciado por depuração sem fio pode precisar ser iniciado novamente.

## 3. Criar o endpoint no Issabel

No painel Issabel, crie um ramal **PJSIP** para o Android.

| Campo | Exemplo / orientação |
| --- | --- |
| Extension | `6000` |
| Display Name | `ANDROID-GSM-LAB` |
| Secret | **Senha forte e exclusiva; não publique** |
| Transport | UDP, apenas na LAN/VPN do teste |
| Max Contacts | `1` |
| Direct Media | Desativado |
| Codecs | `alaw` e `ulaw` inicialmente |
| Qualify Frequency | `0` **somente se OPTIONS não funcionar no gateway** |

Em ambientes com Issabel, nomes e posições desses campos variam. Confirme a porta de escuta PJSIP do seu Asterisk (nem sempre será 5060).

`Qualify Frequency = 0` desabilita as verificações por SIP OPTIONS; o contato pode aparecer como `NonQual`, sem significar que não funciona. Isso **não** é um monitoramento ativo de disponibilidade.

## 4. Configurar o Voice SIP Agent

No aplicativo, ajuste o registro SIP com os dados do endpoint criado:

| Campo | Exemplo |
| --- | --- |
| SIP stack | `pjsua2` |
| SIP transport | `UDP` |
| SIP server / PBX host | IP real do Issabel |
| SIP server port | Porta real do PJSIP |
| SIP user / Extension | `6000` |
| Password | Senha do endpoint `6000` |
| Cellular inbound → SIP target | `1001` |
| Cellular audio | `Direct telephony` para o teste de áudio |
| Mute call audio on this phone | Desativado no teste de áudio |

Os rótulos podem mudar de acordo com a versão. Use **Save & apply**, quando disponível, e confirme o estado de registro e o **Health**. Escolha a SIM de saída quando houver mais de uma.

**Importante:** no laboratório, o ajuste decisivo para o áudio recebido foi permitir o funcionamento de chamadas em **segundo plano** no Android. O modo `Direct telephony` também foi avaliado; não presumir que ele seja necessário em todos os modelos.

## 5. Configurar tronco e rota de saída

Para esta versão do laboratório, foi utilizado um **Custom Trunk** com a seguinte string de discagem:

```text
PJSIP/$OUTNUM$@6000
```

Configure uma **Outbound Route** dedicada:

| Parâmetro | Exemplo |
| --- | --- |
| Route Name | `SAIDA-GSM-LAB` |
| Prefix | `87` |
| Match Pattern | `X.` |
| Prepend | Em branco |
| Trunk Sequence | Tronco GSM de teste |
| Limite de canais do tronco | 1 |

Ao discar `87<DDD><NUMERO>` pelo ramal, o PBX remove o prefixo `87` e encaminha o destino telefônico ao gateway. No SIP INVITE, a parte do usuário da Request-URI deve conter o **número de destino**, e não simplesmente `6000`.

**Segurança:** `X.` aceita diversas combinações numéricas. Utilize-o apenas em laboratório isolado, preferencialmente limitado a ramais e destinos de teste. Não exponha uma rota aberta à internet.

## 6. Configurar o recebimento

No Voice SIP Agent, configure:

```text
Cellular inbound → SIP target = 1001
```

A chamada recebida no chip deve gerar um INVITE para a extensão `1001` do Issabel. O softphone registrado nesse ramal tocará.

O endpoint Android com `context=from-internal` foi usado no teste, mas essa permissão ampla de discagem é inadequada para um cenário produtivo sem restrições adicionais.

## 7. Conferir o registro

No console do Asterisk:

```bash
asterisk -rvvv
```

Em seguida:

```text
pjsip show endpoint 6000
pjsip show contacts
pjsip show endpoint 1001
```

Verifique se os contatos apontam para IPs realmente alcançáveis. Um endereço de interface virtual de Windows no **Contact** do MicroSIP pode impedir que o ramal toque em chamadas recebidas. Corrija a interface SIP do softphone e efetue um novo registro, se necessário.

Próximo: [Testes e diagnóstico](testes-diagnostico.md).
