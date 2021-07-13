<template>
    <div class="info">
        <p class="data">{{ text }}</p>
        <div class="line diagonal" />
        <div class="line" />
        <p class="place">{{ place }}</p>
    </div>
</template>

<script>
    export default {
        name: 'Info',

        props: {
            text: {
                type: String,
                required: true,
                default: 'Thanos snaps his fingers',
            },
            place: {
                type: String,
                required: true,
                default: 'New York',
            },
        },
    }
</script>

<style lang="scss" scoped>

    .info {
        height   : 100px;
        width    : 250px;
        position : relative;
    }

    p {
        position    : absolute;
        color       : white;
        text-shadow : 0px 0px 3px #000000, -1px -1px #000000, 1px 1px #000000;

        &.data {
            left        : 36px;
            bottom      : 30px;
        }

        &.place {
            left        : 45px;
            bottom      : 2px;
            font-size: 14px;
            color: #AD8174;
        }

    }

    .line {
        content  : '';
        height   : 0px;
        border   : 2px solid lavender;
        width    : 100px;
        position : absolute;
        bottom   : 35px;
        left     : 36px;

        &.diagonal {
            width     : 40px;
            left      : 0;
            bottom    : 20px;
            transform : rotate(-45deg);
        }
    }

</style>
