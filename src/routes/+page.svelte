<script lang="ts">
   let payout = 0;
   let spins = 20;
   let pressed = false;

   const add = (x: number) => {
      return {
         title: `+${x}€`,
         func: () => {
            payout += x;
         },
      };
   };

   const take = (x: number) => {
      return {
         title: `-${x}€`,
         func: () => {
            payout -= x;
         },
      };
   };

   const addSpin = {
      title: "+1 Spin",
      func: () => {
         // since one spin is consumed,
         // two are added, such that
         // you end up with a net gain of one spin
         // since (-1) + 2 = 1
         spins += 2;
      },
   };

   const shuffled = (array: Array<any>) => {
      let tempArray = [];

      while (array.length > 0) {
         let idx = Math.floor(Math.random() * array.length);
         tempArray.push(array[idx]);
         array.splice(idx, 1);
      }

      return tempArray;
   };

   //

   let fields = [
      add(10),
      add(10),
      add(5),
      take(5),
      take(5),
      take(1),
      take(1),
      addSpin,
   ];

   fields = shuffled(fields);

   const spin = () => {
      let e = document.getElementById("payout");
      e?.classList.remove("win", "lose");

      void e?.offsetWidth;

      let idx = Math.floor(Math.random() * fields.length);
      for (let child of document.getElementsByClassName("fields")[0].children) {
         if (child.classList.contains("active")) {
            child.classList.remove("active");
         }
      }
      document
         .getElementsByClassName("fields")[0]
         .children[idx].classList.add("active");
      fields[idx].func();
      if (payout < 0) {
         payout = 0;
      }

      if (fields[idx].title === "+1 Spin") {
      } else if (fields[idx].title.startsWith("+")) {
         e?.classList.add("win");
      } else {
         e?.classList.add("lose");
      }
      spins--;

      pressed = true;
      setTimeout(() => {
         pressed = false;
      }, 150);
   };
</script>

<div class="container">
   <h1 id="payout">{payout}€</h1>

   <div class="fields">
      {#each fields as field}
         <p class={"field " + (field.title.startsWith("+") ? "good" : "bad")}>
            {field.title}
         </p>
      {/each}
   </div>

   {#if spins > 0}
      <button id="spin-button" class:pressed on:click={spin}>Spin!</button>
   {/if}

   <p class="spins">Noch {spins} spins</p>
</div>

<style>
   @import url("https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100..900;1,100..900&display=swap");

   :global(*) {
      background: rgb(38, 38, 46);
   }

   .container {
      display: flex;
      flex-direction: column;
      align-items: center;
      width: 100%;
   }
   h1 {
      font-weight: bolder;
      font-size: 70px;
      color: linear;
      background: linear-gradient(0deg, #fe8026, #ffe945);
      -webkit-background-clip: text; /* Clips the background to the text */
      background-clip: text; /* Clips the background to the text */
      -webkit-text-fill-color: transparent;
      margin: 70px;
   }
   .win {
      animation: win 0.6s;
   }
   .lose {
      animation: lose 0.6s;
   }

   @keyframes win {
      0% {
         background: linear-gradient(0deg, #0f911c, #81ea81);
         -webkit-background-clip: text; /* Clips the background to the text */
         background-clip: text; /* Clips the background to the text */
         -webkit-text-fill-color: transparent;
      }
      50% {
         filter: blur(0px);
         transform: scale(120%) translateY(-10px);
      }
      70% {
         background: linear-gradient(0deg, #0f911c, #81ea81);
         -webkit-background-clip: text; /* Clips the background to the text */
         background-clip: text; /* Clips the background to the text */
         -webkit-text-fill-color: transparent;
      }
      100% {
         transform: scale(100%);
      }
   }

   @keyframes lose {
      0% {
         background: linear-gradient(0deg, #910f0f, #eb6060);
         -webkit-background-clip: text; /* Clips the background to the text */
         background-clip: text; /* Clips the background to the text */
         -webkit-text-fill-color: transparent;
      }
      50% {
         filter: blur(0px);
         transform: scale(70%) rotateZ(5deg) translateY(10px);
      }
      70% {
         background: linear-gradient(0deg, #910f0f, #eb6060);
         -webkit-background-clip: text; /* Clips the background to the text */
         background-clip: text; /* Clips the background to the text */
         -webkit-text-fill-color: transparent;
      }
      100% {
         transform: scale(100%);
      }
   }

   h1,
   p,
   button {
      font-family: "Roboto", sans-serif;
      font-optical-sizing: auto;
      font-style: normal;
      font-variation-settings: "wdth" 100;
   }

   p {
      color: white;
   }

   .field:nth-child(1) {
      border-radius: 15px 15px 0px 0px;
   }
   .field:last-child {
      border-radius: 0px 0px 15px 15px;
   }

   p.good,
   p.bad {
      border-width: 1px;
      box-shadow: rgba(0, 0, 0, 0.18) 0px 10px 10px;
      border-style: solid;
      text-align: center;
      transition: 0.2s;
      margin: 0px 0px 0px 0px;
      padding: 10px;
   }

   p.good {
      background: rgb(33, 47, 47);
      border-color: rgb(61, 97, 88);
   }
   p.good.active {
      background: rgb(0, 65, 65);
      border-color: rgb(36, 141, 115);
   }

   p.bad {
      background: rgb(42, 34, 34);
      border-color: rgb(108, 67, 67);
   }
   p.bad.active {
      background: rgb(97, 29, 29);
      border-color: rgb(188, 69, 69);
   }

   .fields {
      display: flex;
      flex-direction: column;
      width: 500px;
   }

   .spins {
      margin-top: 40px;
      color: rgb(171, 171, 189);
   }

   .pressed {
      transform: translateY(10px);
      box-shadow: rgb(161, 96, 11) 0px 0px;
   }
   #spin-button {
      background: linear-gradient(0deg, #f1b40b, #ffe945);
      border-style: none;
      padding: 30px;
      border-radius: 15px;
      font-size: 40px;
      font-weight: 800;
      color: white;
      text-shadow: rgba(121, 71, 6, 0.303) 1px 4px 4px;
      margin-top: 40px;
      transition: all 0.3s;
   }
   #spin-button:not(.pressed) {
      box-shadow: rgb(161, 96, 11) 0px 10px;
   }
</style>
