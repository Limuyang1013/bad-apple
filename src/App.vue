<template>
    <div id="app">
        <canvas id="cv" ref="cv">BAD APPLE</canvas>
        <div id="binaryOutput"></div>
        <video id="video" ref="myVideo" controls @timeupdate="capturePic" @pause="pauseVideo">
            <source src="./source/badapple.mp4" type="video/mp4">
        </video>
        <div id="txt" ref="render"></div>
    </div>
</template>

<script>
    let fancyOutput, canvas

    export default {
        name: 'bad-apple',
        data() {
            return {
                index: 1,
                flag: false,
                playTxt: '播放',
                scale: 1,
                timer: null
            }
        },
        created() {
            this.$nextTick(() => {
                canvas = this.$refs.cv
                fancyOutput = this.$refs.render
            })
        },
        methods: {
            pauseVideo() {
                cancelAnimationFrame(this.timer)
            },
            capturePic() {
                canvas.width = this.$refs.myVideo.videoWidth * this.scale
                canvas.height = this.$refs.myVideo.videoHeight * this.scale
                fancyOutput.width = `${this.$refs.myVideo.videoWidth * this.scale}px`
                fancyOutput.height = `${this.$refs.myVideo.videoHeight * this.scale}px`
                this.drawPic()
                // this.$refs.pic.src = canvas.toDataURL('image/png')

            },
            drawPic() {
                if (this.$refs.myVideo.paused) {
                    return
                }
                canvas.getContext('2d').drawImage(this.$refs.myVideo, 0, 0, canvas.width, canvas.height)
                this.timer = requestAnimationFrame(this.drawPic)
                const imgData = canvas.getContext('2d').getImageData(0, 0, canvas.width, canvas.height)
                this.init(imgData)
            },
            init(imgData) {
                let imgDataArr = imgData.data
                let imgDataWidth = imgData.width
                let imgDataHeight = imgData.height
                let html = '';
                for (let h = 0; h < imgDataHeight; h += 12) {
                    let p = '<p>';
                    for (let w = 0; w < imgDataWidth; w += 6) {
                        let index = (w + imgDataWidth * h) * 4
                        let r = imgDataArr[index]
                        let g = imgDataArr[index + 1]
                        let b = imgDataArr[index + 2]
                        let gray = this.getGray(r, g, b)
                        p += this.toText(gray)
                    }
                    p += '</p>';
                    html += p;
                }
                fancyOutput.innerHTML = html;
            },
            getGray(r, g, b) {
                // 根据rgb值计算灰度，缩放1000倍实现整数算法
                return (r * 299 + g * 578 + b * 114) / 1000
            },
            toText(g) {
                // 根据灰度生成相应字符
                if (g <= 30) {
                    return '#';
                } else if (g > 30 && g <= 60) {
                    return '&';
                } else if (g > 60 && g <= 120) {
                    return '$';
                } else if (g > 120 && g <= 150) {
                    return '*';
                } else if (g > 150 && g <= 180) {
                    return 'o';
                } else if (g > 180 && g <= 210) {
                    return '!';
                } else if (g > 210 && g <= 240) {
                    return ';';
                } else {
                    return '.';
                }
            }
        }
    }
</script>

<style type="text/css">
    #app {
        font-family: simsun,serif;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
        font-size: 12px;
    }

    p {
        height: 12px;
        margin: 0;
        padding: 0;
    }

    #txt {
        display: inline-block;
        text-align: center;
    }
</style>
