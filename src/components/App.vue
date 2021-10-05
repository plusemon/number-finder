<template>
  <div id="app">
    <div class="form">
      <p>
        Searching for =>
        <span :class="{ valid: start_sufix.length == 6 }"
          >01886{{ start_sufix }}</span
        >
      </p>
      <p :class="status == 'Available' ? 'valid' : 'invalid'">{{ status }}</p>
      <input type="number" v-model="start_sufix" />
      <button @click="startSearch">Search</button>
      <!-- <button @click="stopSearch">Stop</button> -->

      <h1>Available Number List</h1>
      <ol>
        <li v-for="number in numbers" :key="number.id">
          {{ number.simNumber }}
        </li>
      </ol>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",

  data() {
    return {
      start_sufix: 6,
      number: null,
      numbers: [],
      isSearching: null,
      status: null,
    };
  },

  methods: {
    startSearch() {
      this.collectingAvailableNumbers();
    },

    collectingAvailableNumbers() {
      if (this.start_sufix.toString().length == 6) {
        let url = `https://da.robi.com.bd/api/v1/check/${this.number}/PREPAID`;
        fetch(url)
          .then((response) => {
            if (response.status === 200) {
              return response.json();
            } else {
              console.log("server error");
            }
          })
          .then((response) => {
            if (response.message == null) {
              this.status = "Available";
              this.numbers.push(response.numbers[0]);
            } else {
              this.status = "Not Available";
            }
            this.start_sufix++;
          })
          .catch((err) => {
            console.log(err);
          });
      } else {
        console.warn(
          "Wrong input value: " + this.start_sufix.toString().length
        );
      }
    },
  },

  watch: {
    start_sufix() {
      if (this.start_sufix.toString().length == 6) {
        this.status = "Checking";
        this.number = `01886${this.start_sufix}`;
        console.log(this.number);
        // setTimeout(() => {
        this.collectingAvailableNumbers();
        // }, 100);
      }
    },
  },
};
</script>

<!-- CSS libraries -->
<style src="normalize.css/normalize.css"></style>

<!-- Global CSS -->
<style>
.form {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 80vh;
}

.form input {
  padding: 10px;
  border-radius: 5px;
  border: 1px solid gray;
}

button {
  margin-top: 10px;
  color: white;
  background: rgb(39, 60, 255);
  padding: 10px 10px;
  border: none;
  outline: none;
  border-radius: 4px;
  cursor: pointer;
}
button:disabled {
  background: rgb(199, 199, 199);
}

ol li {
  color: green;
  font-weight: bold;
  font-size: 20pt;
}

.valid {
  color: green;
  font-size: 20pt;
  font-weight: bold;
}

.invalid {
  color: red;
  font-size: 20pt;
  font-weight: bold;
}
</style>