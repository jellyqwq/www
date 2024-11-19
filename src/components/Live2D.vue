<template >
    <div id="canvas"></div>
</template>

<script setup lang="ts">
import * as PIXI from 'pixi.js';
import { Live2DModel } from 'pixi-live2d-display/cubism4';

// 将 PIXI 暴露到 window 上，这样插件就可以通过 window.PIXI.Ticker 来自动更新模型
window.PIXI = PIXI;

(async function () {
    const app = new PIXI.Application({
        view: document.getElementById('canvas') as HTMLCanvasElement,
    });

    const model = await Live2DModel.from(import('@/assets/Firefly/Firefly.model3.json'));

    app.stage.addChild(model);

    // 变换
    model.x = 100;
    model.y = 100;
    model.rotation = Math.PI;
    model.skew.x = Math.PI;
    model.scale.set(2, 2);
    model.anchor.set(0.5, 0.5);

    // 交互
    model.on('hit', (hitAreas) => {
        if (hitAreas.includes('body')) {
            model.motion('tap_body');
        }
    });
})();

</script>

<style scoped>

</style>