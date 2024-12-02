<script setup>
import {ref,computed} from "vue"
const catList=ref([])

fetch('/data/cats.json')
.then((res)=>res.json())
.then ((data)=>{
  console.log(data);
  catList.value=data.cats
})


const cart=ref([])

const totalPrice = ref(0);

watch(cart, () => {
  // do something...
  let total = 0;
  for(let i = 0; i < cart.value.length; i++) {
    total += cart.value[i].price * cart.value[i].qty;
  }
  totalPrice.value = total;
}, { deep: true });

const addToCart=(cat)=>{
  const itemIdx = cart.value.findIndex((d) => d.name===cat.name)
  if(itemIdx === -1){
    cart.value.push({
    name:cat.name,
    price:cat.price,
    qty:1
  })  

  }else{
    cart.value[itemIdx].qty++
  }
}

const removeFromCart=cartName=>{
  cart.value=cart.value.filter(d => d.name !== cartName.name)
}

</script>

<template>
  <main>
    <pre>{{cart}}</pre>
    <header>
      <nav class="shadow navbar bg-base-100">
        <div class="flex-1">
          <a class="text-xl btn btn-ghost"
            ><i class="fas fa-gem"></i> 賺很大商店</a
          >
        </div>
        <div class="flex-none">
          <ul class="px-1 menu menu-horizontal">
            <li>
              <a
                href="https://github.com/5xTraining/shopping-cat-v3"
                target="_blank"
                ><i class="fa-brands fa-github"></i> GitHub</a
              >
            </li>
          </ul>
        </div>
      </nav>
    </header>

    <section class="container px-6 py-3 mx-auto">
      <p class="px-6 py-2 bg-yellow-400 rounded-full">請以認養取代購買！</p>
      <div
        class="grid grid-cols-1 gap-3 my-2 lg:grid-cols-6 sm:grid-cols-3"
      >
        <!-- cat start -->
        <div         
        v-for = "cat in catList"
        class="shadow card bg-base-100">
          <figure>
            <img :src="`/images/${cat.picture}`" class="select-none" alt="" />
          </figure>
          <div class="card-body">
            <h5 class="card-title">{{cat.name}}</h5>
            <p>{{cat.price}}</p>
            <div class="justify-end card-actions">
              <button  
              @click="addToCart(cat)"
              class="btn btn-primary">
                <i class="fas fa-cat"></i> 認養
              </button>
            </div>
          </div>
        </div>
        <!-- cat end -->


      </div>

      <section class="px-8 mt-12">
        <h2 class="text-3xl font-bold">認養清單</h2>
        <p v-if="cart.length===0">目前沒有認養的貓咪</p>
        <table v-else></table>
        <table class="table my-2">
          <thead>
            <tr class="text-lg">
              <th>名字</th>
              <th>數量</th>
              <th>手續費</th>
              <th>小計</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <!-- item start -->
            <tr  v-for = "item in cart"
            class="text-lg">
              <td>{{item.name}}</td>
              <td>
                <input 
                  type="number"
                  v-model="item.qty"
                  value="1"
                  min="1"
                  class="input input-bordered"
                />
              </td>
              <td>{{item.price}}</td>
              <td>{{item.price * item.qty}}</td>
              <td>
                <button  @click="removeFromCart(item)" class="btn btn-xs btn-primary">
                  <i class="fas fa-trash-alt"></i>
                </button>
              </td>
            </tr>
            <!-- item end -->

          </tbody>
          <tfoot class="text-lg bg-slate-100">
            <tr>
              <td colspan="2"></td>
              <td>總價</td>
              <td class="font-bold">$28.50</td>
              <td></td>
            </tr>
          </tfoot>
        </table>
        <button  @click="cart=[]" class="btn btn-primary">
          <i class="fas fa-baby-carriage"></i> 清空認養清單
        </button>
      </section>
    </section>
  </main>
</template>
