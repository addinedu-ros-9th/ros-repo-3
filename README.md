# 🛫 ARCS: Airport Robot for Customer Service
공항에서 **동행 안내(Leading/Following)**, **자연어 질의응답(LLM)** 을 제공하는 공항 특화 안내 로봇 시스템

---

## 🎥 Quick Demos
> Leading / Following 핵심 동작을 나타냈습니다. **썸네일을 클릭하면 재생**됩니다.

| Leading (동행 안내) | Following (추종) |
| --- | --- |
| [![Leading Demo](/images/leading_play_thumbnail.png)](https://youtu.be/4Kcd3IMIhL8) | [![Following Demo](/images/following_play_thumbnail.png)](https://youtu.be/4ZDxL9DHjOM) |

---
## 0. Development Environment
| 분류           | 사용 기술 |
|----------------|-----------|
| **개발 환경**      | ![Ubuntu](https://img.shields.io/badge/Ubuntu%2024.04-E95420?style=flat&logo=ubuntu&logoColor=white) ![VSCode](https://img.shields.io/badge/VSCode-007ACC?style=flat&logo=visual-studio-code&logoColor=white) |
| **언어**           | ![Python](https://img.shields.io/badge/Python%203.10-3776AB?style=flat&logo=python&logoColor=white) |
| **데이터베이스**   | ![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat&logo=mysql&logoColor=white) |
| **형상 관리**      | ![Git](https://img.shields.io/badge/Git-F05032?style=flat&logo=git&logoColor=white) ![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white) |
| **협업 도구**      | ![Confluence](https://img.shields.io/badge/Confluence-172B4D?style=flat&logo=confluence&logoColor=white) ![Jira](https://img.shields.io/badge/Jira-0052CC?style=flat&logo=jira&logoColor=white) ![Slack](https://img.shields.io/badge/Slack-4A154B?style=flat&logo=slack&logoColor=white) |
| **ROS/시뮬레이션** | ![ROS 2 Jazzy](https://img.shields.io/badge/ROS%202-Jazzy-22314E?style=flat&logo=ros&logoColor=white) |

---
## 1. Overview
**ARCS**는 공항 이용객을 목적지까지 **안전하게 동행 안내**하고, **자연어 Q&A**와 **운영/관리 도구**를 함께 제공하는 로봇 시스템입니다. 

---

## 2. Key Features
- **동행 안내(Leading/Following)**: 목적지 복수 선택, 경로 생성·안내, 이탈·재진입 처리  
- **안전 주행 보조**: 전·후·좌·우 구역 기반 장애물 회피 + **긴급 정지(E-Stop)**  
- **안정 추종(Re-ID)**: 재진입 시에도 **동일 대상 유지**  
- **GUI 이원화**: **User GUI**(현장 조작·경로 선택) / **Admin Console**(상태·정책·충전 복귀)  
- **자연어 Q&A(LLM)**: 경량 LLM + LoRA로 **도메인 질의응답**(주행 제어와 완전 분리)

---

## 3. Team Information
| 이름 | 구분 | 역할 및 담당 업무 |
|---|---|---|
| **김진언** | 팀장 | Tracking, Main Service, Data Service |
| **이동훈** | 팀원 | Chat Service(LLM), Admin GUI, DB |
| **이동연** | 팀원 | User GUI, 통신 인터페이스, 테스트 |
| **유영훈** | 팀원 | Main Controller 주행, SLAM/맵 |

---

## 4. Design

### 4.1. User Requirements
| Stakeholder | 요구사항 | 우선순위 |
|---|---|---|
| USER | 목적지 **Leading** 안내 | HIGH |
| USER | **짐 적재/운반** | HIGH |
| USER | **멀어지지 않고** 추종 | HIGH |
| USER | 방해 최소화 | MEDIUM |
| USER | **질문에 적절히 응답** | MEDIUM |
| ADMIN | **사용 종료 시 자동 복귀** 정책 | HIGH |
| ADMIN | **로봇 상태** 모니터링 | HIGH |
| ADMIN | **사용자 정보** 조회 | MEDIUM |
| ADMIN | 배터리 부족 시 **충전소 복귀** | HIGH |

### 4.2. System Requirements

#### 4.2.1 주행
| ID | NAME | DESCRIPTION | STATUS |
|---|---|---|---|
| SR_1 | 장애물 대응 기능 | 주행 중 장애물(정적: 의자 / 동적: 사람·다른 로봇) 발견 시 회피 및 정지 | ✅ |
| SR_2 | 지도 생성 기능 | 사용 환경 지도 생성 | ✅ |
| SR_3 | 복수 경유지 지정 기능 | 가고 싶은 복수 경유지 지정 | ✅ |
| SR_4 | 경로 생성 기능 | 입력 장소/위치 기반 경로 생성 | ✅ |
| SR_5 | 목적지 경로 추종 기능 | 최적 경로로 목적지까지 이동 | ✅ |

#### 4.2.2 딥러닝
| ID | NAME | DESCRIPTION | STATUS |
|---|---|---|---|
| SR_6 | 거리 유지 기능 | 사용자–로봇 거리 **1.5 m 내** 유지 | ✅ |
| SR_7 | 거리 체크 기능 | 사용자–로봇 거리 **실시간 측정** | ✅ |
| SR_8 | 사용자 추적 기능 | 사용자를 **0.8–1.2 m** 범위에서 추종 | ✅ |
| SR_9 | 음성 명령 기능 | 간단한 음성 명령으로 제어(예: “목적지로 데려다줘”) | ✅ |
| SR_10 | 질의 응답 기능 | 도메인 질문(상품/위치/항공편 등) 응답 | ✅ |
| SR_11 | 다국어 대응 기능 | 한국어/영어/중국어 등 다국어 | ❌ |

#### 4.2.3 알림/알람 기능
| ID | NAME | DESCRIPTION | STATUS |
|---|---|---|---|
| SR_12 | 경고성 알림 기능 | 거리 초과/이탈/복귀 시 음성 안내(Following/Leading 문구 포함) | ✅ |
| SR_13 | 작동 이상 알람 기능 | 네트워크·경로 설정 오류, 배터리 20% 미만 시 안내 | ❌ |
| SR_14 | 정보성 알림 기능 | 배터리/진행상태, 적재량, 남은 거리·시간, 도착 안내 | ✅ |

#### 4.2.4 정보 관리 및 조회
| ID | NAME | DESCRIPTION | STATUS |
|---|---|---|---|
| SR_15 | 사용자 정보 등록/저장 | 탑승권 QR로 사용자 정보 저장 | ✅ |
| SR_16 | 로봇 정보 저장 | 로봇 상태 **주기 저장** | ✅ |
| SR_17 | 사용자 정보 조회 | 과거 사용자 정보 조회(인증 기반) | ✅ |
| SR_18 | 로봇 실시간 상태 조회 | 지도 상 현재 위치/배터리/운행상태 시각화 | ✅ |
| SR_19 | 통계 기능 | 방문 빈도·연령/성별 통계 시각화 | ❌ |

#### 4.2.5 부가 기능
| ID | NAME | DESCRIPTION | STATUS |
|---|---|---|---|
| SR_20 | 짐 보관 보안 | 비밀번호로 잠금/해제 | ❌ |
| SR_21 | 무게 계산 | 적재 물품 총 무게 측정 | ✅ |
| SR_22 | 자동 복귀 | ① 사용자 종료 시 충전소 복귀 ② 경유지 도착 후 무응답 시 복귀 | ✅ |

### 4.3. System Architecture
![System Architecture](/images/system_architecture.png)

### 4.4. Interface Specification (요약)
- **공통 프레임**: Header 0x00 · Length 4B · Command 2B

**4.4.1 User GUI → Main Service → Main Controller (TCP)**  
| Command | Description |
|---|---|
| **CK** | Main Controller에 **경로 요청** |
| **FW / LD** | **Following / Leading** 실행 |
| **PS** | **일시정지** |
| **RT** | **Task 완료 후 충전소 복귀** |
| **ED** | **경로 수정 요청** |
| **KG** | **로드셀 센서 값 요청** |

**4.4.2 Main Controller → Main Service → User GUI (TCP)**  
| Command | Description |
|---|---|
| **AR** | **목적지/경유지 도착** 알림 |
| **MV** | **로봇 상태 데이터** 전송 |

**4.4.3 Admin GUI ↔ Main Service (TCP)**  
| Command | Description |
|---|---|
| **LG** | **관리자 로그인** |
| **RL** | **로봇 실시간 상태 데이터** 요청/응답 |
| **UI** | **사용자 정보 조회** |

> 상세 Payload/에러코드/시퀀스 다이어그램은 `/docs/interface/*` 로 분리 관리 권장.

### 4.5. Data Structure (엔터티 개요)
| 테이블 | 목적 | 핵심 컬럼(예시) |
|---|---|---|
| PLACE_TYPE | 목적지 타입(식당/상점/서비스) | id, name |
| PLACE | 장소(POI) 메타/방문 통계 | id, type_id, name, visitor, total, pos_x, pos_y |
| PRODUCTS | 상점 판매 상품 | id, name, price, place_id |
| TASK_TYPE | 로봇 상태/업무 타입 | id, name |
| ADMIN | 관리자 계정 | id, username, password(hash) |
| USER | 사용자 정보 | id, name, ticket, boarding, departure, gate, sex, age, seat, from, to, robot_id, created |
| ARCS_1 | 로봇 실시간 상태 | id, user, task, leading, loading, pos_x, pos_y, status_time |
| ROUTE | 경로 기록(waypoints) | id, robot_id, pos_x, pos_y |
| ROBOT_LIST | 등록 로봇 목록 | id |

### 4.6. State Diagram
> 공통 흐름 이후 **Leading / Following** 두 가지 분기로 나뉩니다.
#### Common
![State Diagram – Common Flow](images/state_diagram_common.png)  
*Start → Waiting → User Detection → Loading*
#### Leading
![State Diagram – Leading](images/state_diagram_leading.png)  
*Leading → Destination Setting ↔ Stop → Unloading → Charging*
#### Following
![State Diagram – Following](images/state_diagram_following.png)  
*Following ↔ Stop → Unloading → Charging*

### 4.7. GUI Configuration

#### 4.7.1 Stepbar (사용자 흐름)
- **Leading (6단계)**: `Setup → Destination & Plan → Lead → Pause/Resume → Finish → Charging`  
- **Following (5단계)**: `Setup → Follow → Pause/Resume → Finish → Charging`

---
##### 4.7.1.A Leading
| 주요 단계 | 설명 | 클립 |
|---|---|---|
| **1) Setup** | - **ARCS_mode** — *Mode 선택 (Leading / Following)*<br>- **ARCS_qr** — *QR 인증*<br>- **ARCS_load** — *짐 적재 확인* | ![lead_mode](images/lead_mode_10s.gif)<br>![lead_qr](images/lead_qr_10s.gif)<br>![lead_load](images/lead_load_10s.gif) |
| **2) Destination & Plan** | - **ARCS_destinations** — *목적지/경유지 선택*<br>- **ARCS_path** — *경로 생성 완료* | ![lead_destinations](images/lead_destinations_10s.gif)<br>![lead_path](images/lead_path_10s.gif) |
| **3) Lead (안내 중)** | - **ARCS_lead** — *안내 주행 메인 데모*<br>- **ARCS_next** — *다음 웨이포인트 진입*<br>- **ARCS_leave_comeback** — *사용자 이탈/복귀* | ![lead](images/lead_10s.gif)<br>![lead_next](images/lead_next_10s.gif)<br>![lead_leave_comeback](images/lead_leave_comeback_10s.gif) |
| **4) Pause / Resume** | - **ARCS_pause_resume** — *일시정지/재개* | ![lead_pause_resume](images/lead_pause_resume_10s.gif) |
| **5) Finish** | - **ARCS_end** — *세션 종료(도착 안내/하차)*<br>- **(선택) ARCS_return** — *종료 후 복귀 선택 시* | ![lead_end](images/lead_end_10s.gif)<br>![lead_return](images/lead_return_10s.gif) |
| **6) Charging** | - **ARCS_charge** — *충전 스테이션 도킹/충전 시작* | ![lead_charge](images/lead_charge_10s.gif) |

---
##### 4.7.1.B Following
| 주요 단계 | 설명 | 클립 |
|---|---|---|
| **1) Setup** | - **ARCS_mode** — *Mode 선택 (Leading / Following)*<br>- **ARCS_qr**— *QR 인증*<br>- **ARCS_load** — *짐 적재 확인* | ![follow_mode](images/follow_mode_10s.gif)<br>![follow_qr](images/follow_qr_10s.gif)<br>![follow_load](images/follow_load_10s.gif) |
| **2) Follow (추종 중)** | - **ARCS_follow** — *추종 메인 데모*<br>- **ARCS_far_close** — *대상 유실/거리 초과*<br>- **ARCS_avoid** — *회피 로직 활성화* | ![follow](images/follow_10s.gif)<br>![follow_far_close](images/follow_far_close_10s.gif)<br>![follow_avoid](images/follow_avoid_10s.gif) |
| **3) Pause / Resume** | - **ARCS_pause_resume**  — *일시정지/재개*| ![follow_pause_resume](images/follow_pause_resume_10s.gif) |
| **4) Finish** | - **ARCS_end** — *세션 종료(도착 안내/하차)*<br>- **ARCS_return** — *종료 후 복귀 선택 시* | ![follow_end](images/follow_end_10s.gif)<br>![follow_return](images/follow_return_10s.gif) |
| **5) Charging** | - **ARCS_charge** — *충전 스테이션 도킹/충전 시작* | ![follow_charge](images/follow_charge_10s.gif) |

---
## 5. Intelligent Modules

### 5.1. Tracking & Re-ID
- 파이프라인: **사람 탐지 → 다중대상 추적 → 임베딩 기반 Re-ID(유사도)**  
- 효과: 프레임 이탈/재진입 시 **동일 대상 유지**, 군중 속 오인식 감소
 ![tracking](/images/tracking_10s.gif)

**Troubleshooting (요약)**

| 이슈 | 시도한 조치 | 결과/결정 |
|---|---|---|
| 프레임 아웃/재진입 시 ID 변경 | DeepSORT 단독 운용, `max_age` 상향 | **Re-ID 필요** 확인 |
| 백본 속도↔성능 트레이드오프 | ResNet34 → **ResNet18** | **실시간성 확보**, ID 안정성 유지 |

### 5.2. Following Obstacle Avoidance
- 전/후/좌/우 **위험 영역** 정의, 경우별 회피 기동
 ![avoiding](/images/avoiding.png)
 ![avoiding](/images/avoiding_10s.gif)



### 5.3. LLM Service
- **Base**: google/gemma-2b, **Fine-tuning**: LoRA (peft), Adapter: `adapter_model.safetensors`  
- **I/O(JSON)**: `{ "prompt": "...", "response": "..." }`  
- **운영 안전**: 주행 제어와 **프로세스 분리**, 금칙어/지시문 필터링, 응답 길이 제한

---

## 6. Troubleshooting

### 6.1. Cross-Subnet Communication (정적 라우팅)
- **상황**: 서로 다른 서브넷(192.168.0.x ↔ 192.168.5.x) 노트북 간 직접 통신 불가  
- **해결 전략**: 라즈베리파이(**192.168.0.23 / 192.168.5.1**)를 **경유 라우터**로 사용

**네트워크 구성 요약**  
| 노드 | 인터페이스 | IP/Prefix | 비고 |
|---|---|---|---|
| Laptop A | eth0 | **192.168.0.145/24** | 0.x 대역 |
| Laptop B | wlan0/eth0 | **192.168.5.9/24** | 5.x 대역 |
| Raspberry Pi | eth0 | **192.168.0.23/24** | 0.x 게이트웨이 |
| Raspberry Pi | wlan0 | **192.168.5.1/24** | 5.x 게이트웨이 |

**라우팅 설정 절차**  
| Step | 대상 노드 | 목적 | 명령 |
|---|---|---|---|
| 1 | Raspberry Pi | IP 포워딩 활성화 | `sudo sysctl -w net.ipv4.ip_forward=1` |
| 2 | Laptop A(0.145) | 5.x 대역 경로 추가 | `sudo ip route add 192.168.5.0/24 via 192.168.0.23` |
| 3 | Laptop B(5.9) | 0.x 대역 경로 추가 | `sudo ip route add 192.168.0.0/24 via 192.168.5.1` |

**검증**  
| 관점 | 명령 | 기대 결과 |
|---|---|---|
| 경로 확인(A→B) | `ip route get 192.168.5.9` | next hop 192.168.0.23 |
| 경로 확인(B→A) | `ip route get 192.168.0.145` | next hop 192.168.5.1 |
| 트레이스 | `traceroute -n <상대 IP>` | 1 hop에 Pi IP 표시 |

**최종 명령(요약)**

#### A(0.145): 192.168.5.0/24 → via 192.168.0.23
sudo ip route add 192.168.5.0/24 via 192.168.0.23
#### B(5.9):   192.168.0.0/24 → via 192.168.5.1
sudo ip route add 192.168.0.0/24 via 192.168.5.1

### 6.2. Nav2 Path Extraction (Costmap)

- **증상**: `getPathThroughPoses(start, goals)` 호출 시 *state 6* 오류로 경로 생성 실패(동일 좌표를 `GoToPose`로는 정상).
- **원인**: 초기 스폰/맵 리셋 등으로 남은 **global/local costmap 잔여물(인플레이션 버블)** 이 Start–Goal 사이를 가상 장애물로 차단.
- **해결 루틴**: 경로계획 직전 **코스트맵 초기화** → **200–500ms 대기** → **재계획**(동일 증상 시 local → global 순서 재초기화).

**사용 서비스 인터페이스(Nav2)**

| 목적 | 서비스 이름 | 타입 | 요청 예시 |
|---|---|---|---|
| 전체 초기화(글로벌) | `/global_costmap/clear_entirely_global_costmap` | `nav2_msgs/srv/ClearEntireCostmap` | `ros2 service call /global_costmap/clear_entirely_global_costmap nav2_msgs/srv/ClearEntireCostmap {}` |
| 전체 초기화(로컬) | `/local_costmap/clear_entirely_local_costmap` | `nav2_msgs/srv/ClearEntireCostmap` | `ros2 service call /local_costmap/clear_entirely_local_costmap nav2_msgs/srv/ClearEntireCostmap {}` |

**버블이 사라진 맵의 모습**
![inflation](/images/inflation.png)
---

## 7. Limitations

- 군중 밀집/복잡 환경에서 추종 혼잡도↑ → 추가 Re-ID/예측 보강 필요  
- 도메인 지식 부족 시 LLM 환각/누락 가능 → 지식 주입/평가 데이터 확충 필요  
- 네트워크 불안정 시 GUI–로봇 상태 싱크 지연 → Heartbeat/재전송/버퍼링 강화 필요

---

## 8. Conclusion & Future Works

**Conclusion**: Leading/Following, 안정 추종, 안전 주행 보조, Admin/User 분리 운영까지 **현장 가치를 갖춘 통합 시스템**을 구성했습니다.

**Future Works**
1. **성능 메트릭 공개·개선**: P50/P95 지연, 재식별 유지율, 회피 성공률, 정지거리  
2. **LLM 고도화**: 도메인 지식 확장 및 안전성(금칙어/지시문 필터) 강화  
3. **실증**: 실제 공항 환경에서 장기 운영/실증 및 운영 데이터 축적
