<template>
	<div id="app">
    <div class="container">
		<div class="row">
			<h1>{{title}}</h1>
		</div>
		<div class="row">
		<div class="form-group w-100">
			<label for="name">Имя</label>
			<input type="text" id="name" class="form-control" v-model="name">
		</div>
		<div class="form-group w-100">
			<label for="surname">Фамилия</label>
			<input type="text" id="surname" class="form-control" v-model="surname">
		</div>
		<div class="form-group w-100">
			<label for="phone">Телефон</label>
			<input type="text" id="phone" class="form-control" v-model="phone">
		</div>
		<div class="btn btn-success" @click="onSave">Сохранить</div>
		<div class="btn btn-primary" @click="onLoad">Получить</div>
		</div>
		<div class="row mt-1" v-for="(note,index) in notes" :key="note.id">
			<div class="col align-self-center" v-on:click="onEdit(index)" >{{note.name}} {{note.surname}} {{note.phone}}</div>
			<!--<div class="col-4">{{note.surname}}</div>
			<div class="col-4">{{note.phone}}</div> -->
		</div>
	</div>
	</div>
</template>

<script>
export default {
	name: 'app',
	data () {
		return {
			title: "Записная книжка",
			name: "",
			surname: "",
			phone: "",
			notes: []
		}
	  },
	methods: {
		onSave() {
			let note = {
				name: this.name,
				surname: this.surname,
				phone: this.phone
			};
			try{
			this.$http
				.post('http://localhost:3000/notes', note)
				.then(res => this.onLoad());
			// Пытался добавить async и await, но браузер ругался :(
			//	.then(res => console.log(res));
			}catch(err){
				console.log(err);
			};
			//let that = this;
			//setTimeout(function(){
			//	that.onLoad();
			//},3000);
		},
		onLoad() {
			this.$http
				.get("http://localhost:3000/notes")
				.then(res => res.json())
				.then(res => (this.notes = res));
		},
		onEdit(index){
			//console.log(event);
			console.log(event.target.innerText.split(" "));
			console.log(this.notes[index]);
			let toDelete = (event.target.innerText.split(" "));
			let message = prompt("Хотите ли удалить запись? (Y/n)");
			console.log(message);
			if (message == "Y" || message == "y"){
				this.$http
					.delete('http://localhost:3000/notes/'+this.notes[index].id)
					.then(res => this.onLoad());
			}else if(message == "n" || message == "N"){
				let newName = prompt("Введите новое имя");
				let newSurname = prompt("Введите новую фамилию");
				let newPhone = prompt("Введите новое номер телефона");
				//console.log(newName);
				//console.log(newSurname);
				message = {
					name: newName,
					surname: newSurname,
					phone: newPhone
				}
				console.log(message);
				this.$http
					.put('http://localhost:3000/notes/'+this.notes[index].id, message)
					.then(res => this.onLoad());
			}
			let that = this;
			//setTimeout(function(){
			//	that.onLoad();
			//},3000);
		}
	}
  }

</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
