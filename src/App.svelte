<script>
  import { initializeApp } from "firebase/app";
  import { getAuth, signInWithPopup, GoogleAuthProvider } from "firebase/auth";
  import { getDatabase, ref, onValue, set, get } from "firebase/database";

  import { SvelteToast } from "@zerodevx/svelte-toast";
  import Aside from "./components/Aside.svelte";
  import Calls from "./components/Calls.svelte";
  import Contacts from "./components/Contacts.svelte";
  import { toast } from "@zerodevx/svelte-toast";

  import { onMount } from "svelte";

  const firebaseConfig = {
    apiKey: "AIzaSyDiBGN7LzU6hjD4Y-mfvl36L_pyoup5IHk",
    authDomain: "child-application-36adb.firebaseapp.com",
    databaseURL: "https://child-application-36adb-default-rtdb.firebaseio.com",
    projectId: "child-application-36adb",
    storageBucket: "child-application-36adb.appspot.com",
    messagingSenderId: "348413665047",
    appId: "1:348413665047:web:ab54aebe4fbc8632a62152",
    measurementId: "G-76FX0H3MLS",
  };

  let data = {};

  let currentTab = "calls";

  let sendData = null;

  onMount(() => {
    let user = null;

    const app = initializeApp(firebaseConfig);
    const database = getDatabase(app);

    const provider = new GoogleAuthProvider();
    const auth = getAuth();
    let singedin = localStorage.getItem("user");
    if (!singedin) {
      signInWithPopup(auth, provider)
        .then((result) => {
          user = result.user;
          console.log(user.uid);
          localStorage.setItem("user", JSON.stringify(user));
        })
        .catch((error) => {
          const errorCode = error.code;
          const errorMessage = error.message;
          const email = error.customData.email;
          const credential = GoogleAuthProvider.credentialFromError(error);
          console.log(errorCode, errorMessage, email, credential);
        });
    } else {
      user = JSON.parse(singedin);
      // console.log(user);
    }

    const refD = ref(database, user.uid);
    get(refD)
      .then((snapshot) => {
        if (snapshot.exists()) {
          const v = snapshot.val();
          for (const labelV in v) {
            v[labelV] = v[labelV].filter(
              (item) => item != undefined || item != null
            );
          }
          data = { block: [], ...v };
          console.log(data);
          onValue(refD, (snapshotL) => {
            const vL = snapshotL.val();
            for (const labelV in vL) {
              vL[labelV] = vL[labelV].filter(
                (item) => item != undefined || item != null
              );
            }
            data = { block: [], ...vL };
          });
        } else {
          console.log("No data available");
        }
      })
      .catch((error) => {
        console.error(error);
      });

    sendData = () => set(refD, data);

    //modify
    let f = () =>
      set(refD, {
        calls: [
          {
            contact: "Noone",
            date: "15-06-22 20:57",
            duration: "00:00:28",
            number: "+0123123123",
            type: "INCOMING",
          },
          {
            contact: "Noone",
            date: "13-06-22 20:57",
            duration: "00:00:28",
            number: "+0123123123",
            type: "INCOMING",
          },
          {
            contact: "Noone1",
            date: "10-06-22 20:57",
            duration: "00:00:28",
            number: "+0123123124",
            type: "INCOMING",
          },
          {
            contact: "Noone1",
            date: "14-06-22 20:57",
            duration: "00:00:28",
            number: "+0123123124",
            type: "INCOMING",
          },
          {
            contact: "Noone2",
            date: "17-06-22 20:57",
            duration: "00:00:28",
            number: "+0123123125",
            type: "INCOMING",
          },
          {
            contact: "Noone2",
            date: "12-06-22 20:57",
            duration: "00:00:28",
            number: "+0123123125",
            type: "INCOMING",
          },
        ],
        sms: [
          {
            body: "Test Text Send",
            date: "11-06-22 13:53",
            sender: "User1",
            status: "RECEIVED",
          },
          {
            body: "Test Text 123",
            date: "14-06-22 13:53",
            sender: "User2",
            status: "RECEIVED",
          },
          {
            body: "Last Test Text",
            date: "17-06-22 13:53",
            sender: "User3",
            status: "RECEIVED",
          },
        ],
        contacts: [
          {
            name: "Noone",
            number: "+0123123123",
          },
          {
            name: "Noone1",
            number: "+0123123124",
          },
          {
            name: "Noone2",
            number: "+0123123125",
          },
        ],
      });
    // f();
  });

  const blockNumber = (contact) => {
    if (sendData) {
      if (!data.block.includes(contact)) {
        data.block = [...data.block, contact];
        sendData();
        toast.push(`<b>${contact}</b> blocked`);
      } else {
        toast.push(`<b>${contact}</b> is already blocked`, {
          theme: {
            "--toastBackground": "red",
            "--toastColor": "white",
            "--toastBarBackground": "pink",
          },
        });
      }
    }
  };

  const unblockNumber = (contact) => {
    if (sendData) {
      if (data.block.includes(contact)) {
        data.block = data.block.filter((number) => number != contact);
        sendData();
        toast.push(`<b>${contact}</b> unblocked`);
      } else {
        console.log("Attemp to unblock a number that was not blocked");
      }
    }
  };
</script>

<SvelteToast />
<div class="flex flex-col h-screen bg-red-500">
  <header class="bg-blue-500 p-5 text-white font-semibold text-xl">
    Parental Monitoring Application
  </header>

  <div class="flex flex-grow bg-green-500">
    <Aside bind:currentTab />
    <div class="bg-slate-50 w-full">
      {#if currentTab == "calls"}
        <Calls
          bind:calls={data["calls"]}
          bind:block={data["block"]}
          {blockNumber}
          {unblockNumber}
        />
      {:else if currentTab == "contacts/sms"}
        <Contacts
          bind:contacts={data["contacts"]}
          bind:sms={data["sms"]}
          bind:block={data["block"]}
          {blockNumber}
          {unblockNumber}
        />
      {/if}
    </div>
  </div>
</div>

<style>
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
</style>
