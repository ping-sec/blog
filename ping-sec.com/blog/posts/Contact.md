# Contact

I'll be at the [TCM Security](https://tcm-sec.com) booth in the [Noob Village](https://www.noobvillage.org/) for [Defcon](https://defcon.org) 34. Stop by for a custom sticker or business card.

Otherwise find me here:
- [Discord](https://discord.com/users/202932449297039360)
- [LinkedIn](https://www.linkedin.com/in/casey-campbell-a63255264/)
- [X](https://x.com/PINGSEC_)

Get in touch with me using the form below.

<form action="https://formspree.io/f/xvgwlldz" method="POST" class="contact-form">
  <div class="form-group">
    <label for="name">Name</label>
    <input type="text" id="name" name="name" required>
  </div>
  
  <div class="form-group">
    <label for="email">Email</label>
    <input type="email" id="email" name="_replyto" required>
  </div>
  
  <div class="form-group">
    <label for="subject">Subject</label>
    <input type="text" id="subject" name="_subject" required>
  </div>
  
  <div class="form-group">
    <label for="message">Message</label>
    <textarea id="message" name="message" rows="6" required></textarea>
  </div>
  
  <input type="hidden" name="_next" value="/thanks">
  <input type="text" name="_gotcha" style="display:none">
  
  <button type="submit">Send Message</button>
</form>

<style>
.contact-form {
  max-width: 600px;
  margin: 2rem 0;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 600;
}

.form-group input,
.form-group textarea {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid var(--gray);
  border-radius: 4px;
  font-family: inherit;
  font-size: 1rem;
}

.form-group input:focus,
.form-group textarea:focus {
  outline: none;
  border-color: var(--secondary);
}

.contact-form button {
  background-color: var(--secondary);
  color: white;
  padding: 0.75rem 2rem;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.2s;
}

.contact-form button:hover {
  background-color: var(--tertiary);
}
</style>