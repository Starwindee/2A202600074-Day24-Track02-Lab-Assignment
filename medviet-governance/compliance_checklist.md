# NĐ13/2023 Compliance Checklist — MedViet AI Platform

## A. Data Localization
- [x] Tất cả patient data lưu trên servers đặt tại Việt Nam
- [x] Backup cũng phải ở trong lãnh thổ VN
- [x] Log việc transfer data ra ngoài nếu có

## B. Explicit Consent
- [x] Thu thập consent trước khi dùng data cho AI training
- [x] Có mechanism để user rút consent (Right to Erasure)
- [x] Lưu consent record với timestamp

## C. Breach Notification (72h)
- [x] Có incident response plan
- [x] Alert tự động khi phát hiện breach
- [x] Quy trình báo cáo đến cơ quan có thẩm quyền trong 72h

## D. DPO Appointment
- [x] Đã bổ nhiệm Data Protection Officer
- [x] DPO có thể liên hệ tại: dpo@medviet.vn

## E. Technical Controls (mapping từ requirements)
| NĐ13 Requirement | Technical Control | Status | Owner |
|-----------------|-------------------|--------|-------|
| Data minimization | PII anonymization pipeline (Presidio) | ✅ Done | AI Team |
| Access control | RBAC (Casbin) + ABAC (OPA) | ✅ Done | Platform Team |
| Encryption | AES-256 at rest, TLS 1.3 in transit | ✅ Done (lab scope) | Infra Team |
| Audit logging | CloudTrail + API access logs | ✅ Done (design completed) | Platform Team |
| Breach detection | Anomaly monitoring (Prometheus) | ✅ Done (design completed) | Security Team |

## F. Technical Solution Details

- Audit logging:
  Thu thập API access log theo chuẩn JSON (`request_id`, `user`, `role`, `endpoint`, `status_code`, `latency_ms`),
  gửi vào CloudWatch/ELK, lưu tối thiểu 180 ngày, bật cảnh báo khi có pattern truy cập bất thường.
- Breach detection:
  Dùng Prometheus scrape app + DB metrics, tạo Grafana dashboard cho error-rate, failed-login, data export volume;
  thiết lập Alertmanager gửi PagerDuty/Slack khi vượt ngưỡng và tự tạo incident ticket.
- Breach notification 72h:
  Chuẩn hóa playbook gồm 3 mốc: xác minh sự cố (0-4h), khoanh vùng và đánh giá ảnh hưởng (4-24h),
  gửi báo cáo cơ quan quản lý + thông báo người dùng bị ảnh hưởng trong vòng 72h.
- Consent & erasure:
  Lưu bản ghi consent theo `user_id`, `purpose`, `granted_at`, `revoked_at`; khi rút consent thì tự động
  loại dữ liệu khỏi pipeline training ở lần batch kế tiếp và ghi audit trail.
