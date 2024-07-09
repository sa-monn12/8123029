<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>静清ARサンプル（ワイヤーフレーム）</title>
    <script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
    <script src="https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar.js"></script>
    <style>
        body {
            margin: 0;
            overflow: hidden; /* スクロールを無効化 */
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        #controls {
            position: absolute;
            top: 10px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        #controls button {
            margin: 5px;
        }
        .row {
            display: flex;
        }
    </style>
</head>
<body>
    <a-scene embedded arjs="debugUIEnabled:false;">
        <a-assets>
            <a-asset-item id="obj" src="tinker.obj"></a-asset-item>
            <a-asset-item id="mtl" src="obj.mtl"></a-asset-item>
        </a-assets>
        <a-marker type="pattern" url="sei.patt">
            <a-obj-model id="model" src="#obj" mtl="#mtl" scale="0.1 0.1 0.1" rotation="0 180 0" position="0 0 0"></a-obj-model>
        </a-marker>
        <a-entity camera></a-entity>
    </a-scene>
    
    <div id="controls">
        <div class="row">
            <button id="moveUp">↑</button>
        </div>
        <div class="row">
            <button id="moveLeft">←</button>
            <button id="moveDown">↓</button>
            <button id="moveRight">→</button>
        </div>
        <div class="row">
            <button id="moveForward">前進</button>
            <button id="moveBackward">後退</button>
        </div>
        <div class="row">
            <button id="scaleUp">拡大</button>
            <button id="scaleDown">縮小</button>
        </div>
        <div class="row">
            <button id="rotateX">回転X軸</button>
            <button id="rotateY">回転Y軸</button>
            <button id="rotateZ">回転Z軸</button>
        </div>
        <div class="row">
            <button id="reset">初期位置・向きへ戻す</button>
        </div>
    </div>
    
    <script>
        // モデルの初期位置と向き
        const initialPosition = { x: 0, y: 0, z: 0 };
        const initialRotation = { x: 0, y: 180, z: 0 };
        const initialScale = { x: 0.1, y: 0.1, z: 0.1 };

        // モデルの初期設定
        const model = document.getElementById('model');
        model.setAttribute('position', initialPosition);
        model.setAttribute('rotation', initialRotation);
        model.setAttribute('scale', initialScale);

        // キーボードとボタンの操作を統一する関数
        function moveModel(direction) {
            const position = model.getAttribute('position');
            switch(direction) {
                case 'up':
                    position.y += 0.1;
                    break;
                case 'down':
                    position.y -= 0.1;
                    break;
                case 'left':
                    position.x -= 0.1;
                    break;
                case 'right':
                    position.x += 0.1;
                    break;
                case 'forward':
                    position.z -= 0.1;
                    break;
                case 'backward':
                    position.z += 0.1;
                    break;
            }
            model.setAttribute('position', position);
        }

        function scaleModel(direction) {
            const scale = model.getAttribute('scale');
            switch(direction) {
                case 'up':
                    scale.x += 0.1;
                    scale.y += 0.1;
                    scale.z += 0.1;
                    break;
                case 'down':
                    scale.x = Math.max(0.1, scale.x - 0.1);
                    scale.y = Math.max(0.1, scale.y - 0.1);
                    scale.z = Math.max(0.1, scale.z - 0.1);
                    break;
            }
            model.setAttribute('scale', scale);
        }

        function rotateModel(axis) {
            const rotation = model.getAttribute('rotation');
            switch(axis) {
                case 'x':
                    rotation.x += 10;
                    break;
                case 'y':
                    rotation.y += 10;
                    break;
                case 'z':
                    rotation.z += 10;
                    break;
            }
            model.setAttribute('rotation', rotation);
        }

        // 初期位置・向きへ戻す関数
        function resetModel() {
            model.setAttribute('position', initialPosition);
            model.setAttribute('rotation', initialRotation);
            model.setAttribute('scale', initialScale);
        }

        // キーボードのイベントリスナーを設定
        document.addEventListener('keydown', (event) => {
            switch(event.key) {
                case 'ArrowUp':
                    moveModel('up');
                    break;
                case 'ArrowDown':
                    moveModel('down');
                    break;
                case 'ArrowLeft':
                    moveModel('left');
                    break;
                case 'ArrowRight':
                    moveModel('right');
                    break;
                case 'w':
                    moveModel('forward');
                    break;
                case 's':
                    moveModel('backward');
                    break;
                case '+':
                    scaleModel('up');
                    break;
                case '-':
                    scaleModel('down');
                    break;
                case 'x':
                    rotateModel('x');
                    break;
                case 'y':
                    rotateModel('y');
                    break;
                case 'z':
                    rotateModel('z');
                    break;
                case 'Escape':
                    resetModel();
                    break;
                default:
                    return; // 他のキーが押された場合は無視
            }
            event.preventDefault(); // キーのデフォルト動作を無効化（スクロールを防ぐ）
        });

        // ボタンのイベントリスナーを設定
        document.getElementById('moveUp').addEventListener('click', () => moveModel('up'));
        document.getElementById('moveDown').addEventListener('click', () => moveModel('down'));
        document.getElementById('moveLeft').addEventListener('click', () => moveModel('left'));
        document.getElementById('moveRight').addEventListener('click', () => moveModel('right'));
        document.getElementById('moveForward').addEventListener('click', () => moveModel('forward'));
        document.getElementById('moveBackward').addEventListener('click', () => moveModel('backward'));
        document.getElementById('scaleUp').addEventListener('click', () => scaleModel('up'));
        document.getElementById('scaleDown').addEventListener('click', () => scaleModel('down'));
        document.getElementById('rotateX').addEventListener('click', () => rotateModel('x'));
        document.getElementById('rotateY').addEventListener('click', () => rotateModel('y'));
        document.getElementById('rotateZ').addEventListener('click', () => rotateModel('z'));
        document.getElementById('reset').addEventListener('click', resetModel);
    </script>
</body>
</html>
