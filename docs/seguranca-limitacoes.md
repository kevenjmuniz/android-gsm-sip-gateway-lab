# Segurança, privacidade e limitações

[← Voltar ao README](../README.md)

## Limitações

Este material descreve um **laboratório**, e não um gateway celular certificado para produção.

- O laboratório validou **uma chamada celular ativa por aparelho**. A operação simultânea de dois canais no mesmo Android não foi demonstrada.
- Captura e encaminhamento de áudio dependem do modelo, da versão do Android, dos serviços de telefonia e das permissões.
- `Shizuku` iniciado por `Wireless debugging` pode deixar de funcionar após reiniciar o aparelho.
- Não foram validados operação permanente, alta disponibilidade, falha de energia, recuperação automática ou medições de qualidade.
- A operadora pode impor limitações contratuais e técnicas sobre uso de SIM como gateway.

## Regras mínimas de segurança

1. Mantenha SIP e RTP em LAN segregada ou VPN; evite expor PJSIP diretamente à internet.
2. Utilize senhas fortes e únicas para cada endpoint.
3. Restrinja extensões, contextos e rotas de discagem; não deixe uma rota de chamada livre.
4. Defina apenas codecs necessários e verifique o destino da mídia.
5. Não copie o conteúdo de configurações de produção para repositórios públicos.
6. Atualize os aplicativos de suas fontes oficiais.
7. Habilitar opções de depuração que reduzem proteções do sistema deve ser decisão temporária e consciente.
8. Respeite privacidade das conversas, termos da operadora e regras legais pertinentes.

## Política de dados deste repositório

**Proibido publicar:**

- IPs reais, hostnames internos e pontos de entrada VPN;
- números de telefone, identificadores de chamadas ou dados de clientes;
- senhas, tokens, chaves SSH, arquivos de registro SIP e cabeçalhos de autenticação;
- arquivos `.pcap`, gravações, logs e screenshots brutos de ambiente real.

Os IPs `192.0.2.x` apresentados nos tutoriais são endereços de documentação (RFC 5737), **não** endereços funcionais de LAN.

O nome da conta proprietária do GitHub é publicamente visível em um repositório público. A documentação não inclui outros dados pessoais do ambiente.

## Projeto de origem

Este projeto apenas descreve a integração realizada em laboratório. Código e APK do Voice SIP Agent pertencem aos seus respectivos autores: [sparkcyf/android-voice-sip-agent](https://github.com/sparkcyf/android-voice-sip-agent). Consulte a licença e a documentação deles antes de redistribuir ou modificar software.
