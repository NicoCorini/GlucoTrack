# GlucoTrack

> Smart, structured and clinically‑oriented diabetes telemonitoring platform (Academic Project – Software Engineering AY 2024/25 – Final Grade: Top Evaluation)

## 👀 Quick Pitch (For Non‑Technical Visitors)

GlucoTrack helps people with type 2 diabetes stay on track with medical recommendations. Patients log glucose readings, therapy intakes and symptoms; the system highlights risks and doctors see concise trends instead of raw noise. Everything is auditable, clinically structured and designed as a solid base for future extensions (notifications, predictive risk, device integration).

| Healthcare Need / Pain Point          | How GlucoTrack Responds                                                       |
| ------------------------------------- | ----------------------------------------------------------------------------- |
| Missed doses go unnoticed             | Structured therapy schedules + adherence tracking + alert logic               |
| Doctors lose time in raw spreadsheets | Focused dashboards & summarized trends (glycemia, adherence, alerts)          |
| Fragmented patient info               | Unified model: measurements, therapies, comorbidities, risk factors, symptoms |
| Poor traceability                     | Change logs + explicit DTO contracts + auditable state transitions            |
| Over‑notification risk                | Severity mapping + scoped recipients (patient vs patient+doctor)              |

---

## 🚩 Status Snapshot

- Completed: Authentication & role management, patient clinical data domain (glycemic measurements, therapies & schedules, medication intakes, symptoms, reported conditions, risk factors, comorbidities), alert engine (manual + severity mapping), doctor dashboards & patient analytics summary, change log / audit modeling, structured DTO transport layer, initial automated monitoring module skeleton.
- Optional Extensions (Roadmap): Push notifications (FCM), offline cache & sync queue, predictive risk scoring (hypo/hyper trend detection), glucometer / wearable ingestion, ML stratification, contract test harness, advanced monitoring jobs.

---

## 🔥 Value Highlights

- Reduces silent non‑adherence via structured intake + alert pathways.
- Encourages patient self‑management with clear, minimal input surfaces.
- Gives clinicians actionable (not verbose) aggregated data slices.
- Ensures accountability (audit trail + role scoping, no self‑registration).
- Production‑style separation (controllers / DTOs / EF Core models) for maintainability & future scaling.

---

## 🧱 Architecture Overview

```
api/            ASP.NET Core Web API (.NET) – controllers, DTOs, EF Core context
monitoring/     Background monitoring task (analytics & alert generation foundation)
app/            React Native (TypeScript) mobile client – components, hooks, services, models
```

### Backend

- Pattern: Controllers → (service logic layering emergent) → EF Core DbContext.
- DTO layer isolates transport from persistence entities (versionable contract surface).
- Alert engine centralizes severity mapping + recipient resolution.
- Change log service (extensible) for auditable clinical operations.

### Mobile (React Native + TypeScript)

- `models/` typed domain & DTO interfaces.
- `services/` API orchestration (facade + singleton style for error handling / reuse).
- `hooks/` reactive behaviors (focus refresh, derived state helpers).
- `screens/` & `components/` presentational separation.

### Monitoring Module

- Separate process prepared for periodic scans: missing measurements, adherence gaps, symptom escalation, report synthesis (extensible for ML / risk scoring).

---

## 🗄 Data Model (Key Entities)

Users (Patient / Doctor / Admin), Therapies, MedicationSchedules, MedicationIntakes, GlycemicMeasurements, Symptoms, ClinicalComorbidities, RiskFactors, ReportedConditions, Alerts (+AlertTypes +AlertRecipients), ChangeLogs, PatientDoctors.

Integrity via:

- Foreign keys & referential constraints
- Dual validation (API syntactic + semantic rules)
- Non‑destructive (soft) lifecycle strategies for clinical history
- Explicit auditing of key modifications

---

## 📌 Core Functional Domains

| Domain           | Capabilities                                                                   |
| ---------------- | ------------------------------------------------------------------------------ |
| Auth             | Login, role resolution (Admin / Doctor / Patient)                              |
| User Admin       | Create / update / delete users, role assignment                                |
| Patient Data     | Glycemic logs, therapies & schedules, medication intakes, symptoms, conditions |
| Alerts           | Manual glycemia alert creation, unresolved retrieval, resolution workflow      |
| Clinical Context | Aggregated patient info (doctor & patient views)                               |
| Analytics        | Doctor dashboards (trends, open alerts) & patient summaries                    |
| Auditing         | ChangeLogs schema + service hooks (extensible for full diff capture)           |
| Monitoring       | Background task scaffold for periodic adherence / anomaly scans                |

---

## 🌐 Representative Endpoints

| Purpose                       | Method & Path                                 |
| ----------------------------- | --------------------------------------------- |
| Login                         | POST `/Auth/login`                            |
| List users                    | GET `/Admin/users`                            |
| Upsert user                   | POST `/Admin/user`                            |
| Delete user                   | DELETE `/Admin/user?userId=`                  |
| User clinical info            | GET `/Auth/info?userId=`                      |
| Unresolved alerts for user    | GET `/Alert/user-not-resolved-alerts?userId=` |
| All alerts for user           | GET `/Alert/user-alerts?userId=`              |
| Create manual glycemia alert  | POST `/Alert/create-glycemia-alert`           |
| Resolve alert                 | POST `/Alert/resolve-alert?alertRecipientId=` |
| Patient 7‑day glycemia resume | GET `/Patient/glycemic-resume?userId=`        |
| Patient daily clinical resume | GET `/Patient/daily-resume?userId=&date=`     |
| Doctor dashboard summary      | GET `/Doctor/dashboard-summary?doctorId=`     |
| Recent therapies (doctor)     | GET `/Doctor/recent-therapies?doctorId=`      |
| Therapy create / update       | POST `/Doctor/therapy`                        |
| Therapy soft delete           | DELETE `/Doctor/therapy?therapyId=`           |

---

## 🧪 Testing Strategy

| Level            | Stack                                 | Focus                                     |
| ---------------- | ------------------------------------- | ----------------------------------------- |
| Backend Unit     | xUnit                                 | Controllers, validation, alert logic      |
| Mobile Unit      | Vitest + Testing Library              | Hooks, services, pure utilities           |
| Manual Flows     | RN App + API                          | Navigation & end‑to‑end data paths        |
| Future Hardening | Contract Tests (OpenAPI), E2E (Detox) | Backward compatibility & regression guard |

---

## 🔐 Security & Integrity Principles

- Role‑based access (restricted provisioning; no open signup).
- Strong typing end‑to‑end (C# entities ↔ DTOs ↔ TypeScript interfaces).
- Scoped alert recipients by severity (principle of least notification).
- Audit trail modeling ready for export / compliance extensions.
- Roadmap: JWT refresh rotation, secret management hardening, at‑rest encryption integration hook.

---

## 🧩 Selected Design Patterns

| Layer / Concern   | Pattern(s)         | Benefit                                           |
| ----------------- | ------------------ | ------------------------------------------------- |
| Transport         | DTO Segregation    | Decoupled, versionable API contracts              |
| Frontend Services | Facade + Singleton | Centralized error handling & dependency isolation |
| Hooks             | Observer flavor    | Reactive UI updates on focus / data mutations     |
| Monitoring        | Scheduled Worker   | Separation of periodic heavy logic                |
| Auditing          | Change Log Service | Extensible event trail for clinical operations    |

---

## 🚀 Quick Start (Conceptual)

1. Backend
   - Install .NET 8 SDK
   - Configure DB connection string in `appsettings.json`
   - Apply schema / migrations (SQL script provided)
   - `dotnet test` then `dotnet run`
2. Mobile
   - `cd app`
   - `npm install`
   - Set API base URL (env or config)
   - `npm test` then platform run command (`npx react-native run-android` / iOS)
3. Exploration
   - Swagger UI (enable middleware) at `/swagger`

---

## 🔭 Extension Roadmap (Optional Enhancements)

| Area               | Enhancement Idea                                            |
| ------------------ | ----------------------------------------------------------- |
| Notifications      | Push (FCM) + digest batching + escalation policies          |
| Offline Mode       | Local cache + sync queue + conflict resolution policy       |
| Predictive         | Hypo/hyper risk scoring (trend windows + ML classifier)     |
| Device Integration | Glucometer / wearable ingestion (Bluetooth / vendor APIs)   |
| Contract Safety    | Automated backward compatibility tests per API version      |
| Monitoring Depth   | Periodic adherence gap clustering & anomaly detection       |
| Analytics          | Cohort-level dashboards (population segmentation)           |
| Security Hardening | Token refresh rotation, encrypted secrets vault integration |

---

## 📈 Recruiter Value Summary

- Cross‑stack delivery (mobile + backend + background processing design).
- Realistic clinical domain modeling (therapy schedules, adherence logic, alerts).
- Emphasis on data integrity, auditability, versionable contracts.
- Clear separation of concerns & extensibility points (monitoring, ML, notifications).
- Demonstrates roadmap thinking beyond MVP while shipping a complete core.

---

## 🧪 Example Backend Test Snippet

```csharp
[Fact]
public async Task CreateGlycemiaAlert_ReturnsBadRequest_OnInvalidData() {
    var context = GetInMemoryContext();
    var controller = new AlertController(context);
    var result = await controller.CreateGlycemiaAlert(null);
    Assert.IsType<BadRequestObjectResult>(result);
}
```

---

## 🤝 Academic Context

Developed within a Software Engineering course (AY 2024/25). Objective: apply production‑grade architectural and quality practices to a clinically meaningful telemonitoring scenario. Evaluated with highest marks.

---

## 📬 Contact

Open to feedback, collaboration or architectural deep dives. Reach out via LinkedIn.

---

> This README summarizes the completed core; the roadmap lists optional future enhancements, not missing requirements.
