# NĐ13/2023 Compliance Checklist — MedViet AI Platform

## A. Data Localization
- [ ] Tất cả patient data lưu trên servers đặt tại Việt Nam
- [ ] Backup cũng phải ở trong lãnh thổ VN
- [ ] Log việc transfer data ra ngoài nếu có

## B. Explicit Consent
- [ ] Thu thập consent trước khi dùng data cho AI training
- [ ] Có mechanism để user rút consent (Right to Erasure)
- [ ] Lưu consent record với timestamp

## C. Breach Notification (72h)
- [ ] Có incident response plan
- [ ] Alert tự động khi phát hiện breach
- [ ] Quy trình báo cáo đến cơ quan có thẩm quyền trong 72h

## D. DPO Appointment
- [ ] Đã bổ nhiệm Data Protection Officer
- [ ] DPO có thể liên hệ tại: dpo@medviet.vn

## E. Technical Controls (mapping từ requirements)
| NĐ13 Requirement | Technical Control | Status | Owner |
|-----------------|-------------------|--------|-------|
| Data minimization | PII anonymization pipeline (Presidio) | ✅ Done | AI Team |
| Access control | RBAC (Casbin) + ABAC (OPA) | ✅ Done | Platform Team |
| Encryption | AES-256 at rest, TLS 1.3 in transit | 🚧 In Progress | Infra Team |
| Audit logging | CloudTrail + API access logs | ⬜ Todo | Platform Team |
| Breach detection | Anomaly monitoring (Prometheus) | ⬜ Todo | Security Team |

## F. TODO: Điền vào phần còn thiếu
Với mỗi row còn "⬜ Todo", mô tả technical solution cụ thể bạn sẽ implement.

- Audit logging:
  Thu thập API access log theo chuẩn JSON (request_id, user, role, endpoint, status_code, latency_ms),
  gửi vào CloudWatch/ELK, lưu tối thiểu 180 ngày, bật cảnh báo khi có pattern truy cập bất thường.
- Breach detection:
  Dùng Prometheus scrape app + DB metrics, tạo Grafana dashboard cho error-rate, failed-login, data export volume;
  thiết lập Alertmanager gửi PagerDuty/Slack khi vượt ngưỡng và tự tạo incident ticket.
