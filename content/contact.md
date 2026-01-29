---
title: "Contact & Quote"
description: "Request a CNC machining quote or schedule a mechatronics consultation with Future Present Labs in Seattle."
keywords: ["CNC machining quote", "engineering consulting Seattle", "contact Future Present Labs"]
---

<style>
.contact-shell {
  max-width: 880px;
  margin: 0 auto;
  padding: 0 1rem 3rem;
}

.contact-panel {
  background: linear-gradient(160deg, rgba(15, 23, 42, 0.92), rgba(15, 23, 42, 0.75));
  color: #f8fafc;
  border-radius: 1.5rem;
  padding: 2.5rem;
  border: 1px solid rgba(255, 255, 255, 0.08);
  box-shadow: inset 0 0 0 1px rgba(255, 255, 255, 0.05);
  margin: 1rem 0 2.5rem;
}

.contact-panel h1 {
  margin-bottom: 1rem;
}

.contact-panel p {
  margin-bottom: 1.5rem;
  font-size: 1rem;
  line-height: 1.7;
  color: rgba(248, 250, 252, 0.92);
}

.contact-panel-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 0.6rem;
  margin-bottom: 1.5rem;
}

.contact-panel-meta span {
  font-size: 0.85rem;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  padding: 0.3rem 0.9rem;
  border-radius: 999px;
  border: 1px solid rgba(248, 250, 252, 0.3);
  color: rgba(248, 250, 252, 0.8);
}

.contact-grid {
  display: grid;
  gap: 1.5rem;
}

.contact-card {
  background: #fff;
  border: 1px solid rgba(15, 23, 42, 0.05);
  border-radius: 1rem;
  padding: 1.75rem;
  box-shadow: 0 25px 60px rgba(15, 23, 42, 0.08);
}

.contact-card h2 {
  margin-top: 0;
}

.contact-card ul {
  padding-left: 1.2rem;
  margin-bottom: 0;
}

.contact-card p:last-child {
  margin-bottom: 0;
}

.contact-actions-compact {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
  margin-top: 1rem;
}

.contact-direct {
  margin-top: 2rem;
}

@media (min-width: 768px) {
  .contact-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .contact-panel-buttons {
    display: flex;
    gap: 0.75rem;
    flex-wrap: wrap;
  }
}
</style>

<div class="contact-shell">
  <section class="contact-panel">
    <h1>Tell us what you need built</h1>
    <p>We operate from Seattle, WA. Share your context—drawings, deadlines, constraints—and an engineer will respond with the next steps.</p>
    <div class="contact-panel-meta">
      <span>Seattle shop</span>
      <span>Mon–Fri · 9a–6p PT</span>
      <span>NDA-Ready + ITAR Compliant</span>
    </div>
    <p>Prefer to align live? Let us know and we’ll schedule a short call.</p>
  </section>

  <section class="contact-grid">
    <div class="contact-card">
      <h2>How to reach us</h2>
      <ul>
        <li>Upload machinable CAD and drawings for fast quoting.</li>
        <li>Email when you’re refining requirements or need manufacturing feedback.</li>
        <li>Call if security, firmware, or on-site work is involved.</li>
      </ul>
      <div class="contact-actions-compact">
        <a class="quote-button" href="https://quote.fpl.dev" target="_blank" rel="noopener">Start an instant quote</a>
      </div>
    </div>
    <div class="contact-card">
      <h2>What to include</h2>
      <p>Quick context up front helps us confirm pricing and schedules faster.</p>
      <ul>
        <li>Goal for the hardware (test rig, venue build, fixture, etc.).</li>
        <li>Materials, finishes, quantities, and deadlines.</li>
        <li>Links to CAD, photos, or even a phone snap of a sketch.</li>
      </ul>
    </div>
  </section>

  <section class="contact-grid contact-direct" id="contact-info">
    <div class="contact-card">
      <h2>Direct contact</h2>
      {{< contact-info >}}
    </div>
    <div class="contact-card">
      <h2>Logistics & security</h2>
      <p>Pickups happen in Seattle with daily UPS/FedEx drop-offs. Courier runs are available for rush jobs. <br> We operate under NDA regularly. Please note any compliance needs (ITAR, EAR99, CJIS) so we can confirm the right path.</p>
    </div>
  </section>
</div>
