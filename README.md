# Rede de Computadores para o Centro de Pesquisas ABC

Projeto e implementação simulada de uma infraestrutura de rede segmentada, segura e escalável para um centro de pesquisas fictício composto por duas instalações: a **Unidade Administrativa** e o **Campo de Provas**, situado a 4 km de distância.
Projeto de Extensão V — Engenharia da Computação e Ciência da Computação, Universidade Veiga de Almeida (2025/1).

---

## O problema

O Centro de Pesquisas ABC não possuía infraestrutura de rede estruturada. A Unidade Administrativa abriga um laboratório com 21 computadores, 20 salas de pesquisadores, a sala da administração, a sala de equipamentos e o hall de entrada. O Campo de Provas, a 4 km, tem uma sala de experimentação e uma área externa.

O projeto precisava atender a um conjunto de restrições:

- Sub-redes separadas para administração, laboratório, salas dos pesquisadores e rede Wi-Fi
- Pontos do Campo de Provas na **mesma sub-rede** dos pesquisadores, porém com IP automático
- IP dinâmico no laboratório, na administração e no Campo de Provas; IP fixo nos equipamentos dos pesquisadores
- Tráfego Wi-Fi isolado da sub-rede dos pesquisadores
- Cobertura sem fio restrita à Unidade Administrativa
- Servidores concentrados na sala de equipamentos

## A solução

Rede segmentada em **cinco domínios de difusão (VLANs)**, com roteamento inter-VLAN no modelo *router-on-a-stick*:

| VLAN | Sub-rede | Finalidade | Configuração IP |
|---|---|---|---|
| 10 | 192.168.10.0/27 | Administração + catracas do hall | DHCP |
| 20 | 192.168.20.0/27 | Laboratório de computadores | DHCP |
| 30 | 192.168.30.0/26 | Pesquisadores + Campo de Provas | Fixo (pesq.) / DHCP (CP) |
| 40 | 192.168.40.0/24 | Rede sem fio | DHCP |
| 99 | 192.168.99.0/27 | Gerência e servidores | Estático |

Decisões técnicas centrais:

- **Roteamento inter-VLAN** por subinterfaces 802.1Q em um Cisco 2911, com NAT (overload) na saída para a Internet
- **DHCP centralizado** em um único servidor na VLAN 99, com quatro escopos; como requisições DHCP não atravessam sub-redes, foi configurado `ip helper-address` nas subinterfaces do roteador
- **ACL estendida** aplicada na VLAN 40 negando o tráfego do Wi-Fi para a sub-rede dos pesquisadores
- **Enlace de rádio ponto a ponto de 5 GHz** para vencer os 4 km até o Campo de Provas — o UTP está limitado a 90 m e a fibra enterrada se mostrou inviável no custo
- **Dois switches** para os pesquisadores (SW_Pesq e SW_Pesq2), já que os 40 pontos excedem a capacidade de um switch de 24 portas

## Validação

A topologia foi montada e testada no **Cisco Packet Tracer**. Testes realizados e todos atendidos:

- Comunicação entre VLANs (ping laboratório → administração)
- Obtenção automática de endereço via DHCP no laboratório, administração, Campo de Provas e Wi-Fi
- Dispositivos do Campo de Provas recebendo endereço na faixa reservada (.50–.62) dentro da sub-rede dos pesquisadores
- Bloqueio efetivo do tráfego Wi-Fi em direção aos pesquisadores pela ACL
- Subinterfaces do roteador em estado up/up

## Entregas

- Plantas lógica e física da rede, incluindo cobertura Wi-Fi e detalhamento dos racks
- Documentação técnica completa: ligações entre ativos, endereçamento, tabelas de tomadas por ambiente
- Planilha de custos de implantação — total estimado de R$ 51.554,00
- Topologia funcional simulada no Packet Tracer

## Arquivos

```
.
├── Extensao_IV_Desafio_3_Final.pdf   # Relatório final completo
├── DEFESA_2.pdf                      # Desafio 2 — alinhamento com a ODS 9
├── Cronograma_Extensao.pdf           # Desafio 1 — cronograma de atividades
└── Proj_Ext.pkt                      # Topologia do Cisco Packet Tracer
```

Para abrir o arquivo `.pkt` é necessário o **Cisco Packet Tracer 8.x** ou superior, disponível gratuitamente pelo Cisco Networking Academy.

## ODS

O projeto se alinha ao **ODS 9 — Indústria, Inovação e Infraestrutura**, ao estruturar uma rede segmentada com cobertura cabeada e sem fio, conectividade entre unidades geograficamente distantes e gerenciamento centralizado de endereços IP em apoio às atividades de pesquisa e ensino.

## Tecnologias e conceitos

Cisco Packet Tracer · VLANs 802.1Q · Roteamento inter-VLAN (router-on-a-stick) · DHCP e DHCP relay · NAT · ACL estendida · Cálculo de sub-redes (VLSM) · Enlace de rádio ponto a ponto · Cabeamento estruturado (ABNT NBR 14565)

## Equipe

- Felipe Gabriel Ferraz dos Santos
- Giovanna Rios de Lima Dantas
- Letícia dos Reis Prado
- Lucas Rats Ferreira
- Marcelo Naurath Rebello de Faria

**Orientador:** Kilmer Boente
