
Nothin Much





<html>
    <head>
        <style>
            body{
                margin:0px;
            }
        </style>
        <script>
            const canvas = document.createElement("canvas");
            const context = canvas.getContext("2d");

            const RESIZE = document.createEvent("UIEvent");
            RESIZE.initUIEvent("resize");

            window.addEventListener("load", function(){
                document.body.appendChild(canvas);
                window.dispatchEvent(RESIZE);
            },{capture:false, once:true});

            window.addEventListener("resize", function(){
                canvas.width = window.innerWidth;
                canvas.height = window.innerHeight;
                drawMap(cellMap);
            },false);

            window.addEventListener("keydown", function(event){
                console.log(event.which);
                if (event.which === 71){
                    cellMap = greenery(cellMap);
                } else if (event.which === 72){
                    cellMap = revert(cellMap);
                } else if (event.which === 67){
                    cellMap = makeMap();
                }else{
                cellMap = doSimulationStep(cellMap);
                }
                drawMap(cellMap);
            })

            var windowWHRatio = window.innerHeight / window.innerWidth;
            var width = 300;
            var height =  Math.floor(width * windowWHRatio);
            var cellMap = [];
            const cellWidth = window.innerWidth / width;
            var chanceToStartAlive = 0.4;
            var wallLimit = 5;

            fillMap(cellMap);

            function fillMap(arr){
                for(var y = 0; y < height; y ++){
                    for (var x = 0; x < width; x ++){
                        if(Math.random() < chanceToStartAlive){
                            arr.push({x: x,y: y,state: 1});
                        } else {
                            arr.push({x: x,y: y,state: 0});
                        }
                    }
                }
                drawMap(arr);
            }

            function drawMap(arr){
                context.strokeStyle = "black";
                for (let i = 0; i < arr.length; i++){
                    if(arr[i].state === 0){
                        context.fillStyle = "black";
                    } else if (arr[i].state === 1){
                        context.fillStyle = "blue";
                    } else {
                        context.fillStyle = "yellow";
                    }
                    context.fillRect(arr[i].x*cellWidth, arr[i].y*cellWidth, cellWidth, cellWidth);
                    context.strokeRect(arr[i].x*cellWidth, arr[i].y*cellWidth, cellWidth, cellWidth);
                }
            }

            function doSimulationStep(arr){
                var newMap = [];
                var newState = 0;
                for (let i = 0; i < arr.length; i++){
                    if (countNeighbours(arr, i) < wallLimit){
                        newState = 1;
                    } else {
                        newState = 0;
                    }
                    newMap.push({x: arr[i].x,y: arr[i].y,state: newState})
                }
                return newMap;
            }

            function countNeighbours(arr, i){
                var count = 0;
                for (var s = -1; s < 2; s++){
                        for(var j = -1; j < 2; j++){
                            var neighbour_x = arr[i].x + s;
                            var neighbour_y = arr[i].y + j;
                            if(s === 0 && j === 0){

                            } else if (neighbour_x < 0 || neighbour_y < 0 || neighbour_x >= width || neighbour_y >= height){
                            count ++;
                            } else if (arr[i+(j*width) + s].state === 0){
                            count ++;
                            }
                        }
                    }
                return count;
            }

            function greenery(arr){
                var newMap = [];
                var newState = 0;
                for (let i = 0; i < arr.length; i++){
                    if (arr[i].state === 0 && countNeighbours(arr, i) < 7){
                        newState = 2;
                    } else {
                        newState = arr[i].state;
                    }
                    newMap.push({x: arr[i].x,y: arr[i].y,state: newState})
                }
                return newMap;
            }

            function revert(arr){
                var newMap = [];
                var newState = 0;
                for (let i = 0; i < arr.length; i++){
                    newState = (arr[i].state + 1) % 2 ;
                    newMap.push({x: arr[i].x,y: arr[i].y,state: newState})
                }
                return newMap;
            }

        </script>
    </head>
</html>
