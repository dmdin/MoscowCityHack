<script>
  import {settingWindowState} from '../storage.js';
  import Line from "svelte-chartjs/src/Line.svelte"


  let checkMat = false;
  let checkToxic = false;
  let checkPrInfo = false;
  let changeState = false;
  let secondWindowState = false;
  export let status = "good";

  let x = 5;
  let y = 5;
  let z = 5;

  const colors = {
    good: {backgroundColor: 'rgba(43, 192, 22, 0.2)', borderColor: 'rgb(43, 192, 22)'},
    normal: {backgroundColor: 'rgb(245, 171, 0, 0.2)', borderColor: 'rgb(245, 171, 0)'},
    bad: {backgroundColor: 'rgb(234, 43, 31, 0.2)', borderColor: 'rgb(234, 43, 31)'}
  }
  let fT = x * y * z;

  function refT() {
    fT = x * y * z  / 12;
  }

  function getRandomInt(max) {
    return Math.floor(Math.random() * max);
  }

  $: data = [fT, fT*1.2*z, fT*1.4*x, fT*1.3*y, fT*1.5*x, fT*1.9*x, fT*1.8*y, fT*1.7*4];
  let drawData;
  $: {
    data = data.slice(0, 20);
    drawData = {
      labels: ['5s', '10s', '15s', '20s', '25s', '30s', '35s', '40s', '45s', '50s', '55s', '60s'],
      datasets: [{
        data: data,
        tension: 0.0,
        borderColor: colors[status].borderColor,
        backgroundColor: colors[status].backgroundColor,
        pointRadius: 3,
        borderWidth: 2
      }]
    };
  }
  const options = {
    animation: false,
    legend: {
      display: false,
    },
    scales: {
      xAxes: [{
        ticks: {
          fontSize: 10,
          fontColor: 'lightgrey'
        }
      }],
      yAxes: [{
        ticks: {
          beginAtZero: true,
          fontSize: 10,
          fontColor: 'lightgrey',
          maxTicksLimit: 10,
          padding: 25,
        }
      }]
    },
    tooltips: {
      backgroundColor: '#1e90ff'
    }
  }

  let userData = {
    name: "Димочка",
    img: "http://sun9-57.userapi.com/s/v1/ig2/05fFA-EaTmuVYZZr-ffFFe5rerv4-qNX7amMwstHpboPHm3HPWwQruNwP0MkyJNgU3rJxAr-npGMvnFfx0sqK4ng.jpg?size=400x0&quality=96&crop=0,152,960,994&ava=1"
  }
  let name = userData.name;

  function closeWindow() {
    settingWindowState.decrement();
  }

  function saveSettings() {
    settingWindowState.decrement();
  }

  function changeName() {
    changeState = !changeState;
  }

  function saveName() {
    userData.name = name;
    changeState = !changeState;
  }

  function nextWindow() {
    secondWindowState = !secondWindowState;
  }

</script>

<div id="TB_overlay">
</div>

<div class="wrapper">
  <div class="WindowBox">
    {#if !secondWindowState}
      <div class="BoxForSetting">
        <button on:click={closeWindow} class="cancel-button">
          <img src="cancel.svg" class="cancel-icon" alt="cancel-icon"/>
        </button>
        <div class="userInfo">
          <img src={userData.img}>
          {#if !changeState}
            <div class="user-name">
              <h1>{userData.name}</h1>
              <button on:click={changeName} class="edit-button">
                <img src="edit.svg" class="edit-icon" alt="edit-icon"/>
              </button>
            </div>
          {:else}
            <input bind:value={name}>
            <button on:click={saveName} class="saveButton">Сохранить</button>
          {/if}

        </div>

        <div class="oneSettingBox">
          <h3>Скрытие матов</h3>
          <label class="switch">
            <input type="checkbox" checked={checkMat}>
            <span class="slider round"></span>
          </label>
        </div>
        <div class="oneSettingBox">
          <h3>Проверка на токсичность</h3>
          <label class="switch">
            <input type="checkbox" checked={checkToxic}>
            <span class="slider round"></span>
          </label>
        </div>
        <div class="oneSettingBox">
          <h3>Проверка на приватную информацию</h3>
          <label class="switch">
            <input type="checkbox" checked={checkPrInfo}>
            <span class="slider round"></span>
          </label>
        </div>
        <div class="buttonsBox">
          <button on:click={nextWindow} class="main-button">Доп. настройки</button>
          <button on:click={saveSettings} class="main-button">Сохранить</button>
        </div>
      </div>
    {:else}
      <div class="BoxForSetting">
        <div class="oneSetForG">
          <h2>Первый ползунок для x - </h2>
          <input type="number" bind:value={x} on:input={refT}>
        </div>

        <div class="oneSetForG">
          <h2>Первый ползунок для y - </h2>
          <input type="number" bind:value={y} on:input={refT}>
        </div>

        <div class="oneSetForG">
          <h2>Первый ползунок для z - </h2>
          <input type="number" bind:value={z} on:input={refT}>
        </div>
        <Line data={drawData} {options}/>
        <button on:click={nextWindow} class="main-button">Сохранить</button>
      </div>
    {/if}
  </div>
</div>


<style>

  #TB_overlay {
    background-color: #000; /* Чёрный фон */
    height: 100%; /* Высота максимальна */
    left: 0; /* Нулевой отступ слева */
    opacity: 0.50; /* Степень прозрачности */
    position: fixed; /* Фиксированное положение */
    top: 0; /* Нулевой отступ сверху */
    width: 100%; /* Ширина максимальна */
    z-index: 100; /* Заведомо быть НАД другими элементами */
  }

  .wrapper {
    position: relative;

  }

  .WindowBox {
    position: absolute;
    top: 0;
    left: 0;
    height: 500px; /* FIXME подумать, как сделать лучше */
    width: 100%;
    display: grid;
    place-items: center;
    z-index: 101;
  }

  .BoxForSetting {
    padding: 20px;
    border-radius: 10px;
    background-color: #343F48;
    margin-top: 10%;
    height: auto;
    width: 300px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: flex-start;
  }


  h1, h4 {
    color: #fff;
  }

  .edit-icon {
    height: 16px;
    width: 16px;
  }

  .user-name {
    display: flex;
  }

  .edit-button {
    background-color: transparent;
    border: none;
    outline: none;
  }

  .cancel-button {
    display: flex;
    align-items: center;
    justify-content: center;
    align-self: flex-end;
    width: auto;
    height: 16px;
    padding: 0;
    border: none;
    outline: none;
    background-color: transparent;
  }

  .userInfo h1 {
    margin-top: auto;
    margin-bottom: auto;
    margin-left: 10px;
  }

  .userInfo input {
    margin-left: 10px;
    width: auto;
    height: 30px;
    background-color: #1B1B1B;
    outline: none;
    border: none;
    border-radius: 10px;
    color: rgba(255, 255, 255, 0.6);
  }

  .userInfo button {
    height: 30px;
    margin-top: auto;
    margin-bottom: auto;
    margin-left: auto;
  }

  .saveButton {
    border: 1px solid #07E897;
    color: #fff;
    background-color: transparent;
    padding-top: 4px;
    padding-bottom: 4px;
    outline: none;
    border-radius: 24px;
  }

  img {
    height: 100px;
    width: 100px;
    border-radius: 100px;
  }


  .switch {
    margin-top: 0;
    line-height: 0;
  }

  h4 {
    margin-bottom: 5px;
  }

  .oneSettingBox {
    display: flex;
    flex-direction: column;
  }

  .cancel-icon {
    width: 16px;
  }


  .main-button {
    background: #07E897;
    color: #343F48;
    font-weight: 500;
    border-radius: 24px;
    padding-left: 12px;
    padding-right: 12px;
    padding-top: 4px;
    padding-bottom: 4px;
    border: none;
    outline: none;
    margin-top: 15px;
    margin-bottom: 15px;
    /*margin-left: auto;*/
  }

  .buttonsBox {
    display: flex;
    width: 100%;
    justify-content: space-between;
  }

  .oneSetForG {
    display: flex;
  }

  .oneSetForG input {
    width: 50px;
    height: 30px;
    margin-left: 10px;
    margin-top: auto;
    margin-bottom: auto;
  }
</style>
