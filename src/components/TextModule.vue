<template>
    <div class="columns m-3">
            <div class="column">
                <main class="tile 
                is-child
                main
                box
                is-family-code
                has-background-white-bis"
                ref="editor">
                </main>
            </div>
        <StatsModule ref="statsModule"/>
    </div>
</template>
<script>
import StatsModule from './StatsModule.vue';
export default {
    data: () => ({
        text: "",
        charArray: [],
        activeCharId: 0,
    }),
    components: {
        StatsModule
    },
    methods: {

        stringToArray() {
            if (!this.text)
                return [];
            return this.text.split("").map((char, index) => {
                const span = document.createElement("span");
                span.setAttribute("id", index);
                span.innerText = char;
                span.classList.add("char");
                return {
                    id: index,
                    char,
                    span
                };
            });
        },

        async get() {
            const response = await fetch("https://fish-text.ru/get?number=1");
            const result = await response.json();
            return result.text;
        },

        addActive() {
            if (this.charArray[this.activeCharId] === undefined)
                return;
            this.charArray[this.activeCharId].span.classList.add("has-background-info");
        },

        removeActive() {
            this.charArray[this.activeCharId].span.classList.remove("has-background-info");
        },

        keypressHandler(event) {
            const statsModule = this.$refs.statsModule;

            if (!statsModule.isActive) {
                statsModule.startSpeedStat();
            }

            const item = this.charArray[this.activeCharId];

            if (item.char !== event.key) {
                item.span.classList.add("has-text-danger");
                statsModule.addError(item.id, this.charArray.length);
            }else {
                item.span.classList.remove("has-text-danger");
                item.span.classList.add("has-text-success");
                statsModule.incrementCount();

                this.removeActive();
                this.activeCharId++;
                this.addActive();

                if (this.activeCharId === this.charArray.length) {
                this.stop();
                return; // vopros!!
            }
            }
            
        },

        initListener() {
            window.addEventListener("keypress", this.keypressHandler);
        },

        stop() {
            this.$refs.statsModule.stopSpeedStat();
            window.removeEventListener("keypress", this.keypressHandler);
        }

    },
    async created() {
        try {
            this.text = await this.get();
            this.charArray = this.stringToArray();
            this.$refs.editor.innerHTML = "";
            this.charArray.forEach((item) => this.$refs.editor.append(item.span)); //chararray = items
            this.addActive();
            this.initListener();
        }
        catch (e) { }
    },
}
</script>

<style>
.main {
position: relative;
overflow: auto;
display: flex;
flex-wrap: wrap;
}
.char {
display: flex;
width: 13px;
font-size: 21px;
line-height: 1.375;
box-sizing: border-box;
border-radius: 4px;
}
</style>