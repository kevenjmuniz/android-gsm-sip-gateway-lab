# Android GSM/VoLTE ↔ SIP Gateway — Laboratório

Documentação **em português** de um laboratório que utiliza um aparelho Android como ponte entre a rede celular e uma central **Issabel/Asterisk (PJSIP)**, com [Voice SIP Agent](https://github.com/sparkcyf/android-voice-sip-agent), [Shizuku](https://shizuku.rikka.app/) e um softphone SIP.

> **Projeto educacional e independente.** Este repositório contém procedimentos e resultados de testes, **não** hospeda nem modifica o APK original. Não é uma solução homologada para produção.

## Arquitetura

```text
Rede celular (SIM / operadora)
              |
       Android + Shizuku
        Voice SIP Agent
              |
          SIP / RTP
              |
      Issabel / Asterisk
          (PJSIP)
              |
       Softphone SIP
          Ramal 1001
```

O Android registra um endpoint SIP de exemplo **6000**. O softphone usa **1001**. Os números de ramais são fictícios e podem ser adaptados.

## Documentação

| Guia | Conteúdo |
| --- | --- |
| [Instalação e configuração](docs/instalacao-configuracao.md) | Preparação do Android, Shizuku, PJSIP, tronco e rotas |
| [Testes e diagnóstico](docs/testes-diagnostico.md) | Testes de entrada/saída, sngrep, RTP, áudio e correções |
| [Segurança e limitações](docs/seguranca-limitacoes.md) | Privacidade, restrições do lab e cuidados para evoluir |

Os **textos explicativos são em português**; nomes de menus, botões, variáveis e funções são mantidos em inglês quando aparecem assim no aplicativo ou na ferramenta.

## Funcionalidades verificadas no laboratório

| Recurso | Resultado |
| --- | --- |
| Autorização Shizuku e registro SIP | Validado |
| Chamada do ramal para a rede celular | Validado |
| Chamada recebida no chip encaminhada ao ramal | Validado |
| Áudio nos dois sentidos | Validado após ajustes de segundo plano no Android |
| Observação de SIP com sngrep e RTP no Asterisk | Validado |
| Reinicialização totalmente autônoma e operação 24×7 | **Não validado** |
| Múltiplas chamadas no mesmo aparelho | **Não validado / não disponível neste laboratório** |

## Privacidade

Todos os endereços de exemplo usam a faixa de documentação **192.0.2.0/24** (RFC 5737). **Não use esses IPs diretamente em sua LAN:** substitua por endereços reais da sua rede. Não há senhas, números de telefone, dados de usuários, prints de produção ou logs reais neste repositório.

## Referências

- [Voice SIP Agent — projeto original](https://github.com/sparkcyf/android-voice-sip-agent)
- [Shizuku — documentação oficial](https://shizuku.rikka.app/)
- [Asterisk — PJSIP](https://docs.asterisk.org/Configuration/Channel-Drivers/SIP/Configuring-res_pjsip/)

**Escopo:** prova de conceito de laboratório. Verifique regras da operadora e requisitos regulatórios aplicáveis antes de qualquer uso operacional.
