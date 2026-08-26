# Analise de Cabecalho Ethernet

# Estudo Prático: Inspeção de Quadros Ethernet II (Cisco Packet Tracer)

Este repositório documenta a análise prática do cabeçalho de enlace Ethernet II em rede local (LAN). O experimento abrange o mapeamento de endereços físicos, simulação de tráfego de controle (ICMP/ARP) e requisição de aplicação web (HTTP/TCP) em um ambiente simulado no Cisco Packet Tracer.

---

## ⚙️ 1. Mapeamento dos Endereços MAC

Relação dos endereços físicos obtidos nas máquinas via terminal com o comando `ipconfig /all`:

* **PC-A:** `SEU_MAC_A_AQUI`
* **PC-B:** `0003.E430.A016`
* **PC-C:** `0004.9A52.E493`
* **PC-D (Servidor Web):** `00E0.8FAC.C5E7`

---

## 🌐 2. Topologia de Rede

A infraestrutura foi montada interconectando três estações de trabalho e um servidor web através de um Switch Layer 2 Cisco 2960-24TT.

<img width="589" height="467" alt="Print Packet Tracer" src="https://github.com/user-attachments/assets/d15752fb-924c-4523-b7af-9103ae491bc8" />

> *Figura 1: Topologia física desenvolvida no Cisco Packet Tracer.*

---

## 📊 3. Análise Comparativa das PDUs

Valores extraídos das capturas de inspeção de quadros no Packet Tracer (*Outbound PDU Details*):

| Campo do Cabeçalho Ethernet II | Cenário 1 (PC-C ➔ PC-B / ARP-ICMP) | Cenário 2 (PC-D ➔ Host Destino / Web-IPv4) |
| :--- | :--- | :--- |
| **MAC de Destino (Destination MAC)** | `0003.E430.A016` | `00D0.FFD4.9537` |
| **MAC de Origem (Source MAC)** | `0004.9A52.E493` | `00E0.8FAC.C5E7` |
| **EtherType** | `0x0806` (ARP) | `0x0800` (IPv4) |
| **Camada Superior** | ARP / ICMP | IPv4 / TCP (HTTP) |

### **Inspeção Detalhada dos Quadros**

![PDU Cenário 1](print%20simulação%20II.png)
> *Figura 2: Detalhes da PDU no dispositivo PC-B, evidenciando o EtherType `0x0806`.*

![PDU Cenário 2](print%20simulação.png)
> *Figura 3: Detalhes da PDU no dispositivo PC-D, evidenciando o EtherType `0x0800`.*

---

## 🔍 4. Discussão Técnica e Conclusões

### **4.1. Encapsulamento e Independência entre Camadas**
* **Questão:** A estrutura do cabeçalho Ethernet II sofreu alterações ao mudar o tipo de tráfego na rede? O que isso demonstra sobre o modelo em camadas?
* **Análise:** A estrutura física do cabeçalho Ethernet II permanece padronizada independentemente do dado transportado. Isso demonstra a independência entre camadas do modelo OSI/TCP-IP: a Camada de Enlace (L2) atua como um envelope padrão responsável por entregar o quadro entre endereços MAC na rede local, sem se preocupar se o conteúdo (*payload*) é uma resolução de endereços ARP ou dados web HTTP.

### **4.2. Papel do Campo EtherType**
* **Questão:** Qual a função do campo *EtherType* e por que ele variou entre `0x0806` e `0x0800` nas capturas?
* **Análise:** O campo *EtherType* indica à Camada de Enlace qual protocolo da camada superior deve receber a PDU descompactada. O valor `0x0806` indica o protocolo ARP (Resolução de Endereços), enquanto `0x0800` identifica que a carga útil pertence ao protocolo IPv4.

### **4.3. Comportamento e Decisão de Comutação no Switch**
* **Questão:** Durante o envio de dados entre as estações, os quadros são encaminhados para todas as portas do Switch? Como a entrega é gerenciada?
* **Análise:** O Switch encaminha os quadros diretamente para a porta de destino (*unicast*). Ele utiliza sua tabela CAM (*Content Addressable Memory*), associando a porta física ao endereço MAC do dispositivo de destino lido no cabeçalho Ethernet II, evitando o transbordamento (*flooding*) desnecessário na rede.
