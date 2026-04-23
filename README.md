# DR-BDR
Dinâmica de eleição do DR (Designated Router) e BDR (Backup Designated Router) em redes OSPFv2 de multi-acesso em área única.

Visão Geral

Este laboratório demonstra, na prática, a dinâmica de eleição de DR (Designated Router) e BDR (Backup Designated Router) no OSPFv2, conforme definido na RFC 2328.

O foco é evidenciar:

Critérios de eleição (priority + Router ID)
Comportamento não-preemptivo
Impacto das configurações na formação de adjacências

-----------------------------------------------------------------------------------------

Problema: Formação Excessiva de Adjacências

  Em redes multiacesso, todos os roteadores tenderiam a formar adjacências entre si.

  Número de adjacências:

    n(n−1)/2

  Consequências:

  Alto consumo de CPU
  Aumento de tráfego de LSAs
  Maior tempo de convergência

  👉 Solução: eleição de DR e BDR
-----------------------------------------------------------------------------------------
🧱 Topologia
3 Roteadores (R1, R2, R3)
1 Switch L2
Rede: 172.16.0.0/29

R1 ----\
         \
          SW ---- (rede multiacesso)
         /
R2 ----/

R3 ----/

-----------------------------------------------------------------------------------------

🌐 Endereçamento

| Dispositivo | Interface | IP            |
| ----------- | --------- | ------------- |
| R1          | G0/0      | 172.16.0.1/29 |
| R1          | Lo1       | 10.0.0.1/32   |
| R2          | G0/0      | 172.16.0.2/29 |
| R2          | Lo1       | 10.0.0.2/32   |
| R3          | G0/0      | 172.16.0.3/29 |
| R3          | Lo1       | 10.0.0.3/32   |

-----------------------------------------------------------------------------------------

⚙️ Configuração

🔹 R1
enable
configure terminal
hostname R1

interface g0/0
ip address 172.16.0.1 255.255.255.248
no shutdown

interface loopback 1
ip address 10.0.0.1 255.255.255.255

router ospf 10
router-id 3.3.3.3

interface g0/0
ip ospf 10 area 0

🔹 R2
enable
configure terminal
hostname R2

interface g0/0
ip address 172.16.0.2 255.255.255.248
no shutdown

interface loopback 1
ip address 10.0.0.2 255.255.255.255

router ospf 10
router-id 2.2.2.2

interface g0/0
ip ospf 10 area 0

🔹 R3
enable
configure terminal
hostname R3

interface g0/0
ip address 172.16.0.3 255.255.255.248
no shutdown

interface loopback 1
ip address 10.0.0.3 255.255.255.255

router ospf 10
router-id 1.1.1.1

interface g0/0
ip ospf 10 area 0

-----------------------------------------------------------------------------------------

🔍 Verificação

show ip ospf neighbor
show ip ospf interface

-----------------------------------------------------------------------------------------

🧪 Testes Realizados

✔ Eleição padrão
Prioridade default: 1
Resultado:
DR → R1 (maior Router ID)
BDR → R2

✔ Alteração de prioridade
interface g0/0
ip ospf priority 100

Resultado imediato:

❌ Não há mudança no DR

✔ Comportamento não-preemptivo

Mesmo com maior prioridade:

R2 não assume como DR automaticamente

✔ Forçando nova eleição
clear ip ospf process

Executado no R1 (DR atual)

Resultado:

DR → R2
BDR → R1

-----------------------------------------------------------------------------------------

🧠 Principais Conceitos Demonstrados
DR/BDR reduzem adjacências em redes multiacesso
Eleição baseada em:
Prioridade da interface
Router ID (desempate)
OSPF é não-preemptivo
Alterações exigem reinicialização ou queda de adjacência

-----------------------------------------------------------------------------------------

📚 Referências
RFC 2328
Documentação Cisco sobre OSPF
