## vue-adsorb-ball
☝️一个基于vue2.x的吸附式小球组件

### HOW TO USE
```html
<script>
// step 1: install this component
npm i vue-adsorb-ball --save

// step 2: import
import AdsorbBall from 'vue-adsorb-ball';
Vue.use(AdsorbBall);
</script>

<!-- stop 3: render -->
<adsorb-ball 
    :innerPadding="10" dragContentBackgroundColor="#5577FB"
    @click="customEvent">
    <div class="custom-ball" slot="content">
        <img src="xx/yourImgPath"/>
    </div>
</adsorb-ball>
```