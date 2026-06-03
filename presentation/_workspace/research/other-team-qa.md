Here are the answers to the questions requested by each group.


1조

DTN에서는 크게 세 가지 유형으로 우선순위가 결정됩니다.

Expedited : 가장 빨리 보내야 하는 데이터

Normal : 일반적인 수준의 데이터

Bulk : 가장 우선순위가 낮은 데이터

ION의 경우 우선순위를 255단계로 나뉘고 특히 긴급한 데이터를 위해 critical bundle 이라는 개념을 사용합니다.

이 우선순위는 사용자가 임의로 지정하거나, 애플리케이션에서 번들 헤더에 명시할 수 있습니다. DTN 노드는 우선순위를 기반으로 패킷 전송을 제어합니다. 이를 기반으로 노드는 저장 공간이 부족할 경우 자료를 삭제합니다.

In DTN, priority is determined by three main categories: expedited (data that must be sent as quickly as possible), normal (standard level data), and bulk (data with the lowest priority). In the case of ION, priority is divided into 255 levels, and a concept called "critical bundle" is specifically used for highly urgent data. This priority can be arbitrarily designated by the user or specified by the application in the bundle header. DTN nodes control packet transmission based on these priorities.


2조

결론적으로 저희 조의 프로젝트(1. 지구 내 통신)에서는 임의적으로 번들의 크기를 지정하지 않았습니다. 지구 내 통신의 시뮬레이션의 경우, ion-dtn 의 정상적인 작동을 기대하고 이 도구를 사용해보았다는 쪽에 집중하였기 때문에 상세한 설정은 변경하지 않았습니다. 또한 지구 - 화성 시뮬레이션은 여러 문제가 발생하여 현재 미완료된 상태입니다. 그러나 조사해 본 결과 번들의 크기를 지정했을 때 프로토콜 오버헤드 최소화, 저장소 관리 최적화 등의 효과를 가질 수 있다고 합니다. 유익한 지적 감사하며, 미완성된 시뮬레이션을 진행하게 될 경우 이 점을 참고하도록 하겠습니다.

In conclusion, our team did not arbitrarily specify the bundle size for our project (1. Intra-Earth Communications). For the intra-Earth communications simulation, our focus was primarily on testing the proper operation of ion-dtn, so we did not alter the detailed settings. Additionally, the Earth-Mars simulation is currently incomplete due to various issues encountered. However, our research indicates that specifying bundle sizes can help minimize protocol overhead and optimize storage management. We appreciate the insightful feedback and will certainly take this into account when completing the unfinished simulation.


3조

가장 직관적이게 설명드리자면 BP는 형식이고 LTP 는 제어 수단으로써 역할이 분리될 수 있기 때문입니다. BP는 번들을 표준화된 형식으로 캡슐화하는 역할을 수행합니다. LTP는 BP 가 제시한 형식을 어떻게 전송할 지 결정하는 프로토콜입니다. 이 두 가지 역할은 분리될 수 있죠. 이 분리 구조는 한 쪽에서 문제가 발생해도 다른 쪽에 영향을 미치지 않는다는 장점을 가집니다. 또한 LTP 는 심우주 통신에 최적화된 프로토콜입니다. LTP와 BP가 분리되어 있다면 행성 내에서는 기존 통신 프로토콜 (TCP, UDP), 우주 통신에서는 LTP를 사용할 수 있는 효과적인 DTN을 구성할 수 있습니다.

To put it simply, BP (Bundle Protocol) and LTP (Licklider Transmission Protocol) can play separated roles because BP acts as a format while LTP serves as a control mechanism.

BP is responsible for encapsulating bundles into a standardized format, whereas LTP is the protocol that determines how to transmit the format presented by BP. These two roles can be separated. This decoupling structure has the advantage of preventing an issue in one part from affecting the other.

Furthermore, LTP is a protocol optimized for deep space communications. By separating LTP and BP, you can build an effective DTN (Delay-Tolerant Network) that utilizes existing communication protocols (TCP, UDP) within a planet, while using LTP for space communications.


4조

만약 데이터를 잘 받았다는 메세지를 이전 노드에 수신 후 현재 노드가 고장난다면, 그 이전 노드는 재전송 타이머 만료 시 혹은 대체 경로가 확보될 때까지 다시 전송을 수행합니다.

If the previous node receives a message confirming successful data transmission but the current node fails, the previous node will retransmit upon the expiration of a retransmission timer or until an alternative route is secured.


6조

DTN은 NASA와 같은 우주통신과 관련된 기구에서 표준으로 사용되는 네트워크 구조입니다. 따라서 스타링크와 같은 저궤도 위성 통신이 발전한다면 DTN도 고속 전송 환경에 따라 그 기능성과 복잡도가 확장될 것으로 보입니다. 저궤도 위성 통신의 발전은 DTN을 현재 주로 사용되는 프로토콜(TCP, UDP)로 대체하는 결과보다는 DTN을 이용하여 지구-화성 통신, 더 나아가 태양계 네트워크 형성 (SSI) 발전의 결과로 다가올 가능성이 높아 보입니다.  현재 NASA의 동향으로 보았을 때, 위성 통신 기술의 발전에 맞추어 HDTN 개발 등 DTN을 위성 통신 기술에 맞추어 사용하려고 하는 움직임이 많이 보입니다.

DTN (Delay/Disruption Tolerant Network) is a network architecture used as a standard by space communication organizations like NASA. Therefore, as low Earth orbit (LEO) satellite communications such as Starlink advance, the functionality and complexity of DTN are expected to expand in line with high-speed transmission environments. The development of LEO satellite communications is more likely to lead to the advancement of Earth-Mars communications and, furthermore, the formation of the Solar System Internetwork (SSI) using DTN, rather than replacing DTN with currently prevalent protocols (TCP, UDP). Judging by NASA's current trends, there are many movements to adapt DTN for satellite communication technologies, such as the development of HDTN, in step with the advancement of satellite communication technology.


7조

BP의 우선순위는 크게 Expedited(제일 우선), Normal(일반적), Bulk(제일 낮은 순위)로 나뉩니다. 다만 노드가 보관하고 있는 데이터들은 TTL(데이터 수명) 이 명시되어 있어 일정 시간이 지나면 데이터 저장소에서 삭제됩니다. 또한 DTN에서 중요하게 다루어야 할 부분은 대역폭이 아닌 저장소 공간 관리이므로 저장소의 상태를 기반으로 전송률을 제어합니다. 이를 통하여 노드는 저장소의 혼잡도를 낮출 수 있습니다.

In BP (Bundle Protocol), priorities are largely classified into three levels: Expedited (highest priority),  Normal , and Bulk (lowest priority). Additionally, data stored on the nodes is governed by a  TTL (Time-to-Live) , meaning it is automatically deleted from the data storage after a specified period. Furthermore, a crucial aspect that must be addressed in DTNs (Delay-Tolerant Networks) is  storage space management rather than bandwidth . Therefore, nodes control their transmission rates based on storage status, which allows them to effectively reduce storage congestion.


8조

NASA의 DINET 1 실험에서 NASA는 4가지 지표를 통하여 DTN의 성능을 측정하였습니다.

경로 이용률 (Path utilization rate)
전송 가속 비율 (Delivery acceleration ratio)
노드 저장소 활용률 (ION node storage utilization)
다중 경로 이점 (Multipath advantage)

요약하자면, DTN의 주 기능인 Store-and-Forward, BP 를 얼마나 극대화하여 사용하여 최종적인 데이터 회수율을 높였는가를 주로 평가했습니다. 이외에도 여러 가지 지표들 ( 예: 처리량, 지연 시간 등등 ) 을 사용하여 DTN의 성능을 측정할 수 있습니다. 첨언하자면 대면 질문 시간에서도 언급했다시피 DTN의 목적성과 존재 이유를 토대로 어떤 요소들이 어떻게 평가되어야 하는지 직접 고민해 보는 것이 감을 잡는 데 도움이 될 수도 있을 것 같습니다.

In NASA's DINET 1 experiment, NASA measured the performance of DTN using four key metrics:

Path utilization rate
Delivery acceleration ratio
ION node storage utilization
Multipath advantage

In summary, the evaluation primarily focused on how well the core functions of DTN, such as Store-and-Forward and Bundle Protocol (BP), were maximized to increase the ultimate data recovery rate. In addition to these, DTN performance can also be measured using various other metrics (e.g., throughput, latency, etc.). As a side note, and as mentioned during the Q&A session, contemplating what factors should be evaluated and how—based on the purpose and raison d'être of DTN—can be highly helpful in getting a solid grasp of the concept.


9조

DTN은 BP (Bundle Protocol)라는 형식을 이용하여 통신합니다. TCP를 예로 들 때, 하나의 큰 데이터가 여러 세그먼트들로 나뉘어서 전송되는 게 TCP의 특징입니다. BP는 이 세그먼트들을 묶어 하나의 번들로 만듭니다. 만들어진 번들은 한 번에 심우주 네트워크로 전송됩니다. 

In Disruption-Tolerant Networking (DTN), communication relies on the  Bundle Protocol (BP) . While traditional protocols like TCP break large data into smaller segments, BP bundles these segments into a single, cohesive unit called a  bundle . This unified bundle is then transmitted at once across challenging environments, such as deep space networks.


11조

지구 - 화성 간의 통신 거리는 지구 - 지구 통신과 대비했을 때 차이가 매우 큽니다. TCP는 기본적으로 작은 데이터를 여러 번 보내는 구조를 가집니다. 그러나 지구 - 화성 통신과 같이 통신 거리가 매우 클 경우 이 구조는 치명적인 결함을 가집니다 (연결 단절, 지연, 패킷 유실). 예를 들어, 지구 - 화성 거리에서의 두 노드가 연결을 수립하기 위하여 3-way handshake 를 할 경우, 처음에 SYN 신호를 한번 보내는 데에도 큰 시간 비용이 발생합니다. 이런 경우에는 작은 데이터를 여러 번 보내기 보다는 큰 데이터를 한번 보내는 방법이 훨씬 효과적입니다. 

Due to the massive distance between Earth and Mars, standard TCP is highly inefficient because its mechanism of sending small, frequent packets causes severe latency, frequent disconnections, and packet loss. For instance, the 3-way handshake required to establish a connection takes an enormous amount of time just to transmit the initial SYN signal. To overcome these deep-space communication bottlenecks, it is far more effective to bundle data and send large chunks at once rather than making multiple small transmissions.