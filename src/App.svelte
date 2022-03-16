<script>
  import { findPhoneNumbersInText } from 'libphonenumber-js/min';
  import WhatsappIcon from './icons/whatsapp.svg';
  import PasteIcon from './icons/paste.svg';

  let phone;

  async function doPaste() {
    try {
      phone = parse(await navigator.clipboard.readText());
      if (phone) openWhatsapp();
    } catch (error) {}
  }

  function parse(text) {
    const results = findPhoneNumbersInText(text, 'AR');
    console.log(results[0]);
    if (results.length === 0) return biasedParse(text);
    else return results[0].number.number;
  }

  function biasedParse(text) {
    const match = text.match(/\d{4}[\- ]*\d{4}/);
    if (match) {
      const justDigits = getDigits(match[0]);
      return '+5411' + justDigits;
    }
  }

  function handlePaste(event) {
    event.preventDefault();
    const text = (event.clipboardData || window.clipboardData).getData('text');
    phone = parse(text);
    if (phone) openWhatsapp();
  }

  function openWhatsapp() {
    const url = `https://api.whatsapp.com/send/?phone=${getDigits(phone)}`;
    window.location = url;
  }

  function getDigits(text) {
    return text?.replace(/[^\d]/g, '');
  }
</script>

<main>
  <div class="wrapper">
    <h1>Enviá Whatsapp sin agendar</h1>
    <p>Escribí el número de teléfono o pegá del portapapeles y listo.</p>
    <input autofocus bind:value={phone} on:paste={handlePaste} type="tel" />
    <button class="send" on:click={openWhatsapp}>ENVIAR <WhatsappIcon /></button
    >
    <br />
    <button class="paste" on:click={doPaste}>PEGAR <PasteIcon /></button>
  </div>
</main>

<style>
  h1 {
    font-size: 28px;
  }
  main {
    text-align: center;
    max-width: 280px;
    margin: 0 auto;
    display: flex;
    height: 100vh; /*new*/
    align-items: center;
  }

  input {
    text-align: center;
    border: 2px solid #ccc;
    border-radius: 5px;
    padding: 10px 15px;
    width: 100%;
    font-size: 22px;
  }

  button {
    border: 1px solid #fff;
    cursor: pointer;
    border-radius: 5px;
    color: #fff;
    font-size: 24px;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 18px;
    margin: 10px 0;
    width: 100%;
  }

  button :global(svg) {
    margin-left: 15px;
    height: 30px;
  }

  button.send {
    background: #128c7e;
  }

  button.paste {
    background: #966c10;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
