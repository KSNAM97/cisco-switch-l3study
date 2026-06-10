# Lab 01. Routed Port + EIGRP

물리 인터페이스를 `no switchport`로 **Routed Port(L3 포트)** 로 전환하고,
**EIGRP 100** 으로 전 구간을 라우팅하는 실습입니다.

---

## 🗺️ 토폴로지

![Topology](./topology/topology-eigrp.png)

```
                          loopback0: 13.13.10.0/24            loopback0: 13.13.30.0/24
        [WinXP-2]              [SW1]                              [SW3]          [WinXP-3]
         13.13.1.1   e1/3 ──── e0/0                              e0/1 ──── e1/3   13.13.3.1
                          13.13.12.1/24                     13.13.23.3/24
                                 │                                 │
                          13.13.12.2/24 e0/0           e0/1 13.13.23.2/24
                                    └──────── [SW2] ────────┘
                                       loopback0: 13.13.20.0/24
                                              │ e0/2
                                       13.13.22.2/24
                                              │
                                       13.13.22.4/24 fa0/0
                                            [R4]
                                       fa0/1 13.13.44.254/24
                                              │
                                           [SW4] Gi0/0
                                              │ Gi3/3
                                         [WinXP-1] 13.13.44.1
```

---

## 📋 IP 주소 설계

| 장비 | Loopback 0 | 인터페이스 | IP 주소 |
| --- | --- | --- | --- |
| SW1 | 13.13.10.1/24 | e0/0 | 13.13.12.1/24 |
| SW2 | 13.13.20.2/24 | e0/0 / e0/1 / e0/2 | 13.13.12.2 / 13.13.23.2 / 13.13.22.2 (/24) |
| SW3 | 13.13.30.3/24 | e0/1 | 13.13.23.3/24 |
| R4  | - | fa0/0 / fa0/1 | 13.13.22.4 / 13.13.44.254 (/24) |

| PC | IP | Gateway |
| --- | --- | --- |
| WinXP-2 (SW1) | 13.13.1.1/24 | 13.13.1.254 |
| WinXP-3 (SW3) | 13.13.3.1/24 | 13.13.3.254 |
| WinXP-1 (SW4) | 13.13.44.1/24 | 13.13.44.254 |

---

## ⚙️ 구성 순서

```
1) ip routing 활성화
2) Loopback 0 생성 + IP 할당
3) 물리 포트를 no switchport (Routed Port)로 전환 + IP 할당
4) EIGRP 100 구성 (no auto-summary + network)
5) 검증 (neighbor / route / ping)
```

설정 파일은 [`preconfig/`](./preconfig) 폴더 참고.

---

## ✅ 검증

```bash
show ip eigrp neighbors
show ip route eigrp
show ip protocols
ping 13.13.30.3 source 13.13.10.1
```

EIGRP 학습 경로는 라우팅 테이블에 `D` 코드로 표시됩니다.
