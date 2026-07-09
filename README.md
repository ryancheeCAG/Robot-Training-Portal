# Housekeeping Robotics Academy — Robot Training Portal

A single-file web application for training and certifying housekeepers to operate cleaning and patrol robots, built for **Changi Airport Group (CAG)**.

Trainees register (for one or both robots), study the operating know-how, review a facilitated Q&A, then pass a **theory assessment** (component labeling on the robot's exploded diagram + operating questions). A **trainer** then signs off a separate **practical assessment** as Competent (C) or Not Yet Competent (NYC). The trainee's **Certificate** reflects whichever stage(s) they've completed — theory only, or full theory + practical competency — so only one certificate is ever issued per robot, upgraded in place rather than reissued. An **admin dashboard** tracks every trainee across both stages.

---

## Contents

- `Robot Training Portal.dc.html` — the entire application (a self-contained Design Component)
- `assets/` — robot images, videos, certificate robot photos and the CAG logo
- `README.md` — this file

---

## Quick start

Open **`Robot Training Portal.dc.html`** in a modern browser (Chrome, Edge, Safari, Firefox). No build step, server, or install is required — everything runs client-side.

> The three-part Design Component source (template / logic / props) is authored with the project's tooling. The `.dc.html` file itself opens and runs directly in a browser.

---

## The trainee journey

1. **Home** — programme overview and the two robot models.
2. **Register** *(Step 1)* — capture **name, email, mobile, cleaning company** and **robot(s) to be tested on**. Trainees can select **T-Bot, temi, or both** — each selected robot gets its own independent roster entry and certification track.
3. **Learn** *(Step 2)* — "how to operate" know-how for either robot: exploded diagram (labelled, for study), an assembled view video, what each part does, ordered operating steps, safety essentials and daily maintenance. A robot switcher lets trainees view either model regardless of what they registered for.
4. **Q&A** — facilitated, expandable question-and-answer list.
5. **Assessment** *(Step 3)*:
   - **Integrity declaration** — trainee confirms the attempt is their own before starting.
   - **Choose robot** — if registered for both robots, the trainee picks which one to assess now (assessments are taken one at a time).
   - **Quiz** — the robot's exploded diagram with every callout blanked out. Each blank is a dropdown; the trainee names every part, then answers a few operating multiple-choice questions.
6. **Result** — score as a percentage with a full review (correct answers highlighted, wrong choices shown).
   - **Pass (≥ 80%)** → book a practical assessment, or go straight to the certificate (theory-only, upgradeable later).
   - **Fail** → retake the assessment (unlimited attempts) or revisit the training.
7. **Practical assessment** *(Step 4)* — the trainee **books** a date and trainer for their hands-on session. Once booked, they see a confirmation and can update it any time before the trainer signs off. The trainer then records the actual outcome via the **Trainer portal** (see below) — the trainee's screen updates automatically to show **Competent (C)** or **Not Yet Competent (NYC)**.
8. **Certificate** — confirm the name to print, then **Download / Print as PDF**. The certificate's content adapts automatically:
   - **Theory only** → "Certificate of Theory Completion", robot photo shown desaturated, practical status noted as pending/NYC.
   - **Theory + practical Competent** → full "Certificate of Competency", full-colour robot photo, practical sign-off line (trainer + date).
   The same certificate ID is reused when a trainee upgrades from theory-only to fully certified — no new certificate is ever issued for the same robot. Print output is fixed to **A4 landscape**.

---

## Trainer portal

Reachable from the **Trainer** button in the header.

- Demo passcode: **`trainer`** (trainer also picks their name from a list to attribute sign-offs correctly).
- Lists every trainee who has passed theory, with their booked practical date/trainer, deadline, and current status.
- One click marks a trainee **C** (Competent) or **NYC** (Not Yet Competent) — this updates the trainee's certificate and the admin dashboard immediately.

## Admin dashboard

Reachable from the **Admin** button in the header.

- Demo passcode: **`admin`**
- Shows a roster of every registered trainee **per robot** (a trainee registered for both robots gets two rows): name, company, contact, robot, score, attempts, theory status (with validity date), **Practical column showing a C / NYC badge**, and the trainer on record.
- Summary counts for registered / passed / not-yet-passed.
- **Lock dashboard** re-locks it; **Clear all data** wipes local records (with confirmation).

---

## The two robots

**T-Bot — Toilet Cleaning Robot** (12 components)
Control Interface Panel · LiDAR Sensor Module · Articulated Manipulator Arm (4-DOF) · Tool Change Interface · End-Effector (Mop/Brush Head) · Main PCB & Communications Module · Aluminum Extrusion Chassis Frame · Status Indicator LED Strips · Li-ion Battery Power Pack · Lower Chassis Base Plate · Omnidirectional Mecanum Wheels (x4) · Brushless DC Drive Motors

**temi — Patrol / Delivery Robot** (8 components)
Display Tablet · Logitech C922 CV Camera · Circular Shelf Tier (x3) · Upper CV Compartment (Vision System) · NVIDIA Jetson AGX Vision Computer · Li-ion Backup Battery (CV Power) · Vertical Structural Supports · Base with Wheels & Drive System

Scoring per robot:
- **T-Bot** — 12 labels + 4 questions = 16 items; 80% ⇒ **13 correct** to pass.
- **temi** — 8 labels + 4 questions = 12 items; 80% ⇒ **10 correct** to pass.

---

## Data & persistence

This is an **in-browser prototype** — no server or database. Trainee records, progress and scores are stored in the browser's `localStorage` on the device being used:

- `cag_rt_current` — the current trainee, their registered robot(s), and which robot's flow (assessment/practical/certificate) is currently active.
- `cag_rt_roster` — the full roster shown in the admin and trainer dashboards. Each entry is keyed by **email + robot**, so a trainee registered for both robots has two independent records (theory score, practical booking, C/NYC outcome, trainer, certificate ID) that progress separately.

Because storage is per-device/per-browser, records do **not** sync across devices. To move to production, replace the `localStorage` read/write calls (`componentDidMount`, `persistCurrent`, `persistRoster`, `upsertRoster`, `findRosterEntry`) with API calls to a real backend (see "Going to production" below).

---

## Configuration (tweaks)

Three settings are exposed as editable props on the component:

- **Pass threshold** — the percentage required to pass theory (default **80%**).
- **Organization name** — issuer shown on the certificate/footer (default *Changi Airport Group*).
- **Programme name** — the certificate/programme title.

---

## Customising content

All training material lives in the component's logic as plain data and is safe to edit:

- `robotData.tbot` / `robotData.temi` — each robot's intro, part roles, operating steps, safety, maintenance, the exploded-diagram blanks (with on-image positions) and the multiple-choice questions.
- `faqData` — the Q&A list.
- `trainerNames` — the list of trainers selectable at practical booking and at trainer login.

The exploded-view blanks are positioned as percentage coordinates over each diagram; adjust `x`, `y`, `w`, `h` per blank if a diagram is swapped. Certificate robot photos are `assets/t-bot_cert_nobg.png` and `assets/temi_cert.png` (background-removed/blended product shots, distinct from the exploded diagrams and home-page thumbnails).

---

## Going to production

- Swap the `admin` and `trainer` demo passcodes and move authentication server-side (ideally with real trainer accounts, not a shared passcode).
- Wire the roster to a real backend (e.g. a small API in front of a database) for cross-device tracking, so admins/trainers on different machines see the same live roster.
- Consider notifying trainees by email when their practical result is recorded, and when their theory certification is nearing its 12-month expiry.
- The learning/Q&A copy is **drafted placeholder content** derived from the diagrams — review and replace with your official material before rollout.

*Prototype for Changi Airport Group — Housekeeping Robotics Academy.*
