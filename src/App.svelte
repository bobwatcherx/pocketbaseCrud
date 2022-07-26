<script>
  import PocketBase from 'pocketbase';
const client = new PocketBase('http://localhost:8090');

let loginstate = {
  email:"",
  password:""
}
let state = {
  car_name:"",
  price:""
}
let resultdata = []
async function loginbtn(){
  const authData = await client.Users.authViaEmail(
    loginstate.email,loginstate.password);
  console.log(authData)
  if(authData){
    getallcoll()
  }
}
async function getallcoll(){
  const resultList = await client.Records.getList("carcol", 1, 50);
  console.log(resultList)
  resultdata = resultList.items
}

async function deleteitem(r){
  await client.Records.delete("carcol", r.id);
}

async function edititem(r){
  let promptcar = prompt("update name",r.car_name)
  let promptprice = prompt("update price",r.price)
  const record = await client.Records.update("carcol", r.id, {
    car_name: promptcar,
    price:promptprice
});
}
async function addnewitem(){
  const record = await client.Records.create("carcol", {
   car_name: state.car_name,
    price:state.price
});
  console.log(record)
}

</script>
<div>
  <h1>login</h1>
  email: <input type="text" bind:value={loginstate.email} name="">
  <br>
  password: <input type="password"  bind:value={loginstate.password}  name="">
  <br>
  <button
  on:click={loginbtn}
  >
    login
  </button>
  <br>
  <hr>
<!-- INPUT ADD NEW CAR -->

{#if resultdata.length  > 0}
 <h1>Add new CAR</h1>
  car_name: <input type="text" bind:value={state.car_name} name="">
  <br>
  price: <input type="text"  bind:value={state.price}  name="">
  <br>
  <button
  on:click={addnewitem}
  >
    add new
  </button>
  <br>
{/if}
<!-- EDIT and DELETE -->
{#each resultdata as r}
<li>
  <button
  on:click={edititem(r)}
  >edit</button>
  {r.car_name} - {r.price}
  <button
  on:click={deleteitem(r)}
  >delete</button>
</li>

{/each}
</div>