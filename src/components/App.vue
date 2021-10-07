<template>
  <div id="app">
    <div class="form">
      <div class="left-side">
        <div class="operators">
          <button :class="operator == 'robi' ? 'active':'inactive' " @click="setOperator('robi')">Robi</button>
          <button :class="operator == 'airtel' ? 'active':'inactive' " @click="setOperator('airtel')">Airtel</button>
        </div>

        <div class="input_area">
          <span>{{ operator == 'airtel' ? '01601':'01886' }}</span>
          <input type="number" v-model="start_sufix" />
          <button @click="startSearch()">Search</button>
        </div>
      </div>
      <div style="display: block">
        <h1>Available Number List</h1>
        <ol>
          <li v-for="number in numbers" :key="number.simNumber">
            <span :class="{ valid: number.available }">{{
              number.simNumber
            }}</span>
          </li>
        </ol>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",

  data() {
    return {
      start_sufix: 6,
      operator:'robi',
      number: null,
      numbers: [
        // {
        //   simNumber: 1234567,
        //   available: true,
        // },
      ],
      isSearching: null,
      status: null,
    };
  },

  methods: {
    setOperator(value){
      this.operator = value
    },

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
              this.numbers.push({
                simNumber: this.number,
                available: true,
              });
            } else {
              this.status = "Not Available";
              this.numbers.push({
                id: this.number,
                available: false,
                simNumber: this.number,
              });
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
  flex-direction: row;
  justify-content: space-around;
  align-items: center;
  background: rgb(239 255 250);
  height: 100vh;
}

.left-side {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.operators button{
 margin: 0 20px;
}

.input_area {
  display: flex;
  margin-top: 20px;
}

.form input {
  padding: 10px 4px;
  outline: none;
  border: 2px solid rgb(226, 226, 226);
  font-size: 18px;
}

.input_area span {
  padding: 10px 10px;
  background: rgb(226 226 226);
  border-radius: 5px 0 0 5px;
  font-size: 20px;
}

button {
  color: white;
  background: rgb(195 90 221);
  padding: 10px 10px;
  border: none;
  outline: none;
  cursor: pointer;
  border-radius: 5px;
}

button.active {
  background: #0099d3;
}

button.inactive {
  background: #cdcdcd;
}

.input_area button {
  border-radius: 0 5px 5px 0;
}
button:disabled {
  background: rgb(199, 199, 199);
}

.form ol {
  height: 400px;
  background: rgb(138 255 219);
  overflow: overlay;
}

ol li {
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