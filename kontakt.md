---
layout: page
title: Kontakt oss
description: Ta kontakt, så tar vi kontakt med deg så fort vi kan. Vi ser frem til å høre fra deg!
---

<section class="section">
  <div class="container">
    <div class="contact-layout">

      <div class="contact-info">
        <h3>Vi hører fra deg</h3>
        <p>Ta kontakt, så tar vi kontakt med deg så fort vi kan. Vi ser frem til å høre fra deg!</p>
        <br>
        <p><strong>Snekkerstig AS</strong></p>
        <p>📍 Norge</p>
        <p>📞 Ring oss for gratis befaring</p>
        <p>✉️ post@snekkerstig.no</p>
        <br>
        <p style="color:#999; font-size:0.88rem;">
          Skjemaet er koblet til <a href="https://formspree.io" target="_blank" rel="noopener noreferrer">Formspree.io</a>.
          Erstatt <code>YOUR_FORM_ID</code> i <code>kontakt.md</code> med din faktiske Formspree-ID for å aktivere e-postlevering.
        </p>
      </div>

      <div>
        <form class="contact-form" action="https://formspree.io/f/YOUR_FORM_ID" method="POST">
          <div class="form-group">
            <label for="name">Navn *</label>
            <input type="text" id="name" name="name" required placeholder="Ditt fulle navn">
          </div>
          <div class="form-group">
            <label for="email">E-post *</label>
            <input type="email" id="email" name="email" required placeholder="din@epost.no">
          </div>
          <div class="form-group">
            <label for="phone">Telefon</label>
            <input type="tel" id="phone" name="phone" placeholder="+47 000 00 000">
          </div>
          <div class="form-group">
            <label for="service">Tjeneste</label>
            <select id="service" name="service">
              <option value="">Velg tjeneste...</option>
              <option value="baderom">Baderomsløsning</option>
              <option value="oppussing">Oppussing</option>
              <option value="totalrenovering">Totalrenovering</option>
              <option value="annet">Annet</option>
            </select>
          </div>
          <div class="form-group">
            <label for="message">Melding *</label>
            <textarea id="message" name="message" required placeholder="Beskriv prosjektet ditt..."></textarea>
          </div>
          <button type="submit" class="btn btn-primary" style="width:100%;">Send melding</button>
        </form>
      </div>

    </div>
  </div>
</section>
