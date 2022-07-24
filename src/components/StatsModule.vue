<template>
    <div class="column is-3">
        <div class="box mb-5">
           <p class="title">Скорость</p>
           <p class="subtitle">{{speedStat}} з/м</p>
       </div>
       <div class="box">
           <p class="title">Точность</p>
           <p class="subtitle">{{accuracyStat}} %</p>
       </div>
    </div>       
</template>

<script>
    export default {
        data: () => ({
            isActive: false,
            interval: 0,
            secCount: 0,
            charCount: 0,
            errorIds: new Set(),
            accuracyStat: 100,
            speedStat: 0
        }),
        methods: {

            addError(id, arrSize) {
                this.errorIds.add(id);
                const accuracy = 100 - Math.ceil(this.errorIds.size / arrSize * 100);
                this.accuracyStat = accuracy;
            },

            startSpeedStat() {
                this.isActive = true;
                    this.interval = setInterval(() => {
                    this.secCount++;
                    const minutes = this.secCount / 60;
                    const speed = (this.charCount / minutes).toFixed(0);
                    this.speedStat = speed;
                }, 1000);
            },

            stopSpeedStat() {
                this.isActive = false;
                clearInterval(this.interval);
            },

            incrementCount() {
                this.charCount++;
            }
        }
    }
</script>