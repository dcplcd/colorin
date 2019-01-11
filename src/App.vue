<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <div id="color-bttn-container">
      <button class="color-bttn crimson" value="crimson"/>
      <button class="color-bttn darkorange" value="darkorange"/>
      <button class="color-bttn gold" value="gold"/>
      <button class="color-bttn seagreen" value="seagreen"/>
      <button class="color-bttn mediumblue" value="mediumblue"/>
      <button class="color-bttn indigo" value="indigo"/>
      <button class="color-bttn black selected" value="black"/>
      <a id="clear"><i class="fas fa-trash-alt"></i></a>
    </div>
    <canvas id="canvas" width="504px" height="504px"></canvas>
    <div id="menu">
      <a id="save"><i class="fas fa-save"></i></a>
      <!-- <a id="send"><i class="far fa-kiss-wink-heart"></i></a> -->
    </div>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue';

export default {
  name: 'app',
  // components: {
  //   HelloWorld,
  // },
};


document.addEventListener('DOMContentLoaded', () => {
  const canvas = document.getElementById('canvas');

  const context = canvas.getContext('2d');

  let clickX = [];
  let clickY = [];
  let clickDrag = [];
  let clickColor = [];
  let paint;
  let curColor = 'black';
  let curColorBttn;

  Array.from(document.getElementsByClassName('color-bttn')).forEach(
    (bttn) => {
      if (bttn.value === 'black') {
        curColorBttn = bttn;
      }
      bttn.addEventListener('click', () => {
        curColor = bttn.value;
        curColorBttn.classList.remove('selected');
        bttn.classList.add('selected');
        curColorBttn = bttn;
      });
    },
  );

  const addClick = (x, y, dragging) => {
    clickX.push(x);
    clickY.push(y);
    clickDrag.push(dragging);
    clickColor.push(curColor);
  };

  const redraw = () => {
    context.clearRect(0, 0, context.canvas.width, context.canvas.height);

    context.lineJoin = 'round';
    context.lineWidth = 10;

    clickX.forEach((click, i) => {
      context.beginPath();
      if (clickDrag[i]) {
        context.moveTo(clickX[i - 1], clickY[i - 1]);
      } else {
        context.moveTo(clickX[i] - 1, clickY[i]);
      }
      context.lineTo(clickX[i], clickY[i]);
      context.closePath();
      context.strokeStyle = clickColor[i];
      context.stroke();
    });
  };

  canvas.addEventListener('mousedown', (e) => {
    const mouseX = e.pageX - canvas.offsetLeft;
    const mouseY = e.pageY - canvas.offsetTop;

    paint = true;
    addClick(mouseX, mouseY);
    redraw();
  });

  canvas.addEventListener('mousemove', (e) => {
    if (paint) {
      const mouseX = e.pageX - canvas.offsetLeft;
      const mouseY = e.pageY - canvas.offsetTop;
      addClick(mouseX, mouseY, true);
      redraw();
    }
  });

  canvas.addEventListener('mouseup', () => {
    paint = false;
  });

  canvas.addEventListener('mouseleave', () => {
    paint = false;
  });

  document.getElementById('clear').addEventListener('click', () => {
    context.fillStyle = 'white';
    context.fillRect(0, 0, canvas.width, canvas.height);
    canvas.width = canvas.width;
    clickX = [];
    clickY = [];
    clickDrag = [];
    clickColor = [];
  });

  document.getElementById('save').addEventListener('click', (e) => {
    if (window.navigator.msSaveBlob) {
      window.navigator.msSaveBlob(canvas.msToBlob(), 'drawing');
      e.preventDefault();
    } else {
      e.currentTarget.setAttribute('download', 'drawing');
      e.currentTarget.setAttribute('href', canvas.toDataURL());
    }
  });

  document.getElementById('send').addEventListener('click', () => {
    // const imgSrc = canvas.toDataURL("image/png");
  });
});
</script>

<style lang="scss">
// #app {
//   font-family: 'Avenir', Helvetica, Arial, sans-serif;
//   -webkit-font-smoothing: antialiased;
//   -moz-osx-font-smoothing: grayscale;
//   text-align: center;
//   color: #2c3e50;
//   margin-top: 60px;
// }

body {
  margin: 0;
  padding: 0;
}

button {
  &:active, &:focus {
    outline: none;
  }
}

#app {
  width: 100vw;
  height: 100vh;
  box-sizing: border-box;
  padding: 48px 0;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

#canvas {
  position: relative;
  width: 504px;
  height: 504px;
  // border: solid 1px black;
  box-shadow: 0 2px 10px rgba(0, 0, 0, .1);
}

#menu {
  display: flex;
  flex-direction: column;
}

#save, #send {
  cursor: pointer;
  font-size: 50px;
  margin: 0 0 12px 24px;
  text-decoration: none;
  text-align: center;
  color: dimgray;
  opacity: .5;
  transition: .3s;

  &:hover {
    opacity: 1;
  }
}

#color-bttn-container {
  width: 20px;
  margin: 0 24px 0 0;
}

.color-bttn {
  cursor: pointer;
  border: 0;
  width: 20px;
  height: 20px;
  padding: 0;
  margin: 0 0 12px;
  border-radius: 50%;
  box-sizing: border-box;
  border: solid 1px rgba(0, 0, 0, 0);
  transition: .3s;

  &:hover {
    box-shadow: inset 0 0 0 2px #fff;
    border: solid 1px rgba(0, 0, 0, 0);
  }

  &.selected {
    box-shadow: inset 0 0 0 3px #fff;
    border: solid 1px rgba(0, 0, 0, 0);
  }

  &.crimson {
    background-color: crimson;
  }

  &.darkorange {
    background-color: darkorange;
  }

  &.gold {
    background-color: gold;
  }

  &.seagreen {
    background-color: seagreen;
  }

  &.mediumblue {
    background-color: mediumblue;
  }

  &.indigo {
    background-color: indigo;
  }

  &.black {
    background-color: black;
  }
}

#clear {
  cursor: pointer;
  display: block;
  width: 100%;
  font-size: 20px;
  text-align: center;
  color: dimgray;
  opacity: .5;
  transition: .3s;
  margin: 12px 0 0;

  &:hover {
    opacity: 1;
  }
}

@import url('https://use.fontawesome.com/releases/v5.6.3/css/all.css');
</style>
