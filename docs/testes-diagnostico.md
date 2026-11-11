# Testes e diagnóstico

[← Voltar ao README](../README.md)

## 1. Validar a sequência de chamadas

1. Confirme que o Android está registrado como `6000`.
2. Confirme que o MicroSIP está registrado como `1001`.
3. **Saída:** no ramal `1001`, disque o prefixo `87` seguido de um número de teste autorizado.
4. **Entrada:** de outro telefone, ligue para o SIM do Android e verifique se `1001` toca.
5. Atenda e teste a voz **nos dois sentidos**.
6. Repita com o Android desbloqueado e depois com a **tela bloqueada**.

## 2. SIP com sngrep

No servidor autorizado que observa o tráfego SIP:

```bash
sngrep -d any
```

Para restringir a captura, substitua os marcadores por IPs reais:

```bash
sngrep -d any 'host <IP_ANDROID> or host <IP_SOFTPHONE>'
```

Fluxo esperado de uma chamada atendida:

```text
INVITE → 100 Trying → 180 Ringing → 200 OK → ACK
                                             ...RTP...
                                        BYE → 200 OK
```

Um `401 Unauthorized` inicial seguido de autenticação pode ser o desafio SIP normal. Se o ramal não toca, verifique para qual **Contact** o Issabel envia o `INVITE` e se há alguma resposta.

O `180 Ringing` enviado ao Android **não prova** que o softphone realmente recebeu o INVITE.

## 3. Diagnosticar RTP com Asterisk

Entre no CLI:

```bash
asterisk -rvvv
```

Ative temporariamente:

```text
rtp set debug on
```

Durante a chamada, procure linhas semelhantes às seguintes (**IPs de documentação**):

```text
Got  RTP packet from 192.0.2.85:<PORTA_RTP>
Sent RTP packet to   192.0.2.71:<PORTA_RTP>
Got  RTP packet from 192.0.2.71:<PORTA_RTP>
Sent RTP packet to   192.0.2.85:<PORTA_RTP>
```

Desative o debug:

```text
rtp set debug off
```

**Importante:** tráfego RTP bidirecional não comprova voz audível: o payload pode conter silêncio. É necessário testar som nos dois sentidos, configuração de microfone/alto-falante e integração de áudio celular do Android.

## 4. Soluções identificadas no laboratório

| Sintoma | Observação e solução |
| --- | --- |
| Shizuku alerta sobre permissões ADB | Revisar autorização e restrições de depuração do fabricante |
| Android retorna `404` ao chamar diretamente o endpoint `6000` | O gateway espera um número celular de destino; use a rota e o tronco corretamente |
| Chamada chega no Issabel, mas MicroSIP não toca | O **Contact** do softphone apontava para uma interface virtual inacessível; corrigir o IP/interface no MicroSIP e registrar novamente |
| O ramal atende, mas não há voz | Confirmar RTP e as opções `Cellular audio`, `Mute call audio on this phone` e permissões de segundo plano |
| Funciona com tela ligada, mas falha bloqueado | Autorizar chamadas/atividade em segundo plano, rever economia de bateria e restrições do fabricante |
| Contato aparece como `NonQual` | SIP OPTIONS está desativado; isso não é uma falha de registro por si só |

No teste, **as permissões para chamadas em segundo plano resolveram o áudio nas chamadas recebidas**. Esse resultado pode não se reproduzir em outros Androids.

## 5. Inspeção opcional de pacotes

Uma captura RTP/SIP pode ser feita no PBX (somente com autorização):

```bash
tcpdump -i any -s 0 -w /tmp/chamada-lab.pcap 'host <IP_ANDROID> and udp'
```

Pare com `Ctrl+C`. No Wireshark, consulte **Telephony → RTP → RTP Streams**, se a captura/codec permitir. Um arquivo `.pcap` pode conter áudio de chamadas e dados sensíveis: **nunca o publique sem anonimização e autorização de todos os envolvidos**.

## 6. Testes ainda recomendados

- Reiniciar Android e confirmar se o Shizuku precisa ser reiniciado.
- Deixar a tela bloqueada por períodos maiores e testar chamadas.
- Verificar estabilidade de registro, bateria e Wi-Fi.
- Testar desligamento/retorno da rede celular.
- Verificar comportamento ao receber outra ligação com uma chamada já ativa.

Próximo: [Segurança e limitações](seguranca-limitacoes.md).
