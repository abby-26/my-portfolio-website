<template>
  <div id="contact">
    <h1 id="title-contact" class="text-center">Contact</h1>
  </div>

  <div class="container-fluid pt-5 mb-4 text-center">
    <div class="row justify-content-center align-items-start">
      <div class="col-lg-6 mb-4">
        <iframe 
          src="https://www.google.com/maps/embed?pb=!1m16!1m12!1m3!1d3861.8631640399085!2d121.05315172457199!3d14.549816578319032!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!2m1!1sMarket*21%20Market*21!5e0!3m2!1sen!2sph!4v1751516610277!5m2!1sen!2sph" 
          class="map-frame"
          allowfullscreen=""
          loading="lazy"
          referrerpolicy="no-referrer-when-downgrade"
        ></iframe>
      </div>

      <div class="col-lg-6 mb-4">
        <form @submit.prevent="submitForm" id="maps" class="px-md-3">
          <div class="mb-3">
            <input type="text" v-model="name" class="form-control mb-3" placeholder="First Name M.I. Last Name" />
          </div>

          <div class="mb-3">
            <input type="email" v-model="email" required class="form-control mb-3" placeholder="Email" />
          </div>

          <div class="mb-3">
            <textarea v-model="message" class="form-control mb-3" placeholder="Message" rows="8"></textarea>
          </div>

          <div class="mt-4 d-flex justify-content-between align-items-center">
            <div class="d-flex">
              <a href="https://www.linkedin.com/" target="_blank" class="d-inline-block mr-2">
                <img src="/images/LinkedIn.png" width="30" height="30" />
              </a>
              <a href="https://about.gitlab.com/" class="d-inline-block mr-2">
                <img src="/images/gitlab.png" width="30" height="30" />
              </a>
              <a href="https://github.com/" class="d-inline-block">
                <img src="/images/github.png" width="30" height="30" />
              </a>
            </div>

            <button id="button" type="submit" class="btn btn-submit rounded-pill px-4 py-2">
              Submit
            </button>
          </div>

          <!-- Align reCAPTCHA right -->
          <div class="mt-3 d-flex justify-content-end">
            <div ref="recaptchaContainer" style="width: 304px;"></div>
          </div>

        </form>

      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import { Notyf } from "notyf";
import 'notyf/notyf.min.css';

const notyf = new Notyf();

// Add the web3form access key here
const WEB3FORMS_ACCESS_KEY = "84de304d-ad77-4a53-bd5e-f78ef10e9df0";

const subject = "New message from Portfolio Contact Form";

const name = ref("");
const email = ref("");
const message = ref("");

const isLoading = ref(false);

const submitForm = async () => {
  if(!recaptchaToken.value) {
    notyf.error('Please verify that you are not a robot.');
    return;
  }

  isLoading.value = true;
  try {
    const response = await fetch("https://api.web3forms.com/submit", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        Accept: "application/json",
      },
      body: JSON.stringify({
        access_key: WEB3FORMS_ACCESS_KEY,
        subject: subject,
        name: name.value,
        email: email.value,
        message: message.value
      }),
    });
    const result = await response.json();

    if (result.success){
      console.log(result);
      isLoading.value = false;
      notyf.success("Message Sent!");
    }
  } catch (error) {
    console.log(error);
    isLoading.value = false;
    notyf.error("Failed to send message");
  } finally {
    //Reset captcha after submit or error
    resetRecaptcha();
  }
}

const SITE_KEY = '6LeYFNwrAAAAANq3H-wyoxScze04m6WzvOS24mhI';

const recaptchaContainer = ref(null);
const recaptchaWidgetId = ref(null);
const recaptchaToken = ref('');

function onRecaptchaSuccess(token) {
  recaptchaToken.value = token;
}

function onRecaptchaExpired() {
  recaptchaToken.value = '';
}

function renderRecaptcha() {
  if(!window.grecaptcha) {
    console.error('reCAPTCHA not loaded');
    return;
  }

  recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
    sitekey: SITE_KEY,
    size: 'normal', //compact
    callback: onRecaptchaSuccess,
    'expired-callback': onRecaptchaExpired,
  });
}

//Function to reset reCaptcha
function resetRecaptcha() {
  if (recaptchaWidgetId.value !== null) {
    window.grecaptcha.reset(recaptchaWidgetId.value);
    recaptchaToken.value = '';
  }
}

onMounted(() => {
  const interval = setInterval(() => {
    if(window.grecaptcha && window.grecaptcha.render) {
      renderRecaptcha();
      clearInterval(interval);
    }
  }, 100);

  onBeforeUnmount(() => {
    clearInterval(interval);
  });
});
</script>
