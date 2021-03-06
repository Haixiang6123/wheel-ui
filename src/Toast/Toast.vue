<template>
    <div class="wrapper" :class="toastClasses">
        <div ref="toast" class="toast">
            <div class="message">
                <slot v-if="!enableHtml"></slot>
                <div v-else v-html="$slots.default[0]"></div>
            </div>

            <div class="line" ref="line"></div>

            <span
                v-if="closeButton"
                class="close"
                @click="onClickClose">
            {{closeButton.text}}
        </span>
        </div>
    </div>
</template>

<script>
    export default {
        name: "w-toast",
        props: {
            autoClose: {
                type: [Boolean, Number],
                default: 3,
                validator(value) {
                    return value === false || typeof value === 'number';
                }
            },
            closeButton: {
                type: Object,
                default() {
                    return {
                        text: 'Close',
                        callback: undefined
                    }
                }
            },
            enableHtml: {
                type: Boolean,
                default: false
            },
            position: {
                type: String,
                default: 'top',
                validator(value) {
                    return ['top', 'middle', 'bottom'].indexOf(value) >= 0;
                }
            }
        },
        computed: {
            toastClasses() {
                return {[`position-${this.position}`]: true};
            }
        },
        mounted() {
            this.execAutoClose();
            this.setHeightForLine();
        },
        methods: {
            execAutoClose() {
                if (this.autoClose) {
                    setTimeout(() => {
                        this.close();
                    }, this.autoClose * 1000);
                }
            },
            setHeightForLine() {
                this.$nextTick(() => {
                    this.$refs.line.style.height = this.$refs.toast.getBoundingClientRect().height + 'px';
                });
            },
            close() {
                this.$el.remove();
                this.$emit('close');
                this.$destroy();
            },
            onClickClose() {
                this.close();
                if (this.closeButton && typeof this.closeButton.callback === 'function') {
                    this.closeButton.callback(this);
                }
            }
        }
    }
</script>

<style scoped lang="scss">
    $font-size: 14px;
    $toast-min-height: 40px;
    $toast-bg: rgba(0, 0, 0, 0.75);
    $box-shadow: rgba(0, 0, 0, 0.5);
    $animation-duration: 300ms;

    @keyframes fade-in {
        0% { opacity: 0; }
        100% { opacity: 1; }
    }

    @keyframes slide-up {
        0% { opacity: 0; transform: translateY(100%) }
        100% { opacity: 1; transform: translateY(0%); }
    }
    @keyframes slide-down {
        0% { opacity: 0; transform: translateY(-100%) }
        100% { opacity: 1; transform: translateY(0%) }
    }

    .wrapper {
        position: fixed;
        left: 50%;
        transform: translateX(-50%);
        z-index: 999;

        &.position-top {
            top: 0;
            .toast {
                border-top-left-radius: 0;
                border-top-right-radius: 0;
                animation: slide-down $animation-duration;
            }
        }
        &.position-middle {
            top: 50%;
            transform: translateX(-50%) translateY(-50%);
        }
        &.position-bottom {
            bottom: 0;
            .toast {
                border-bottom-left-radius: 0;
                border-bottom-right-radius: 0;
                animation: slide-up $animation-duration;
            }
        }
    }

    .toast {
        display: flex;align-items: center;min-height: $toast-min-height;
        font-size: $font-size;line-height: 1.8;color: white;background: $toast-bg;
        border-radius: 4px;box-shadow: 0 0 3px 0 $box-shadow;padding: 0 16px;
        animation: fade-in $animation-duration;

        .message {
            padding: 8px 0;
        }

        .close {
            padding-left: 16px;
            flex-shrink: 0;
        }

        .line {
            height: 100%;
            border-left: 1px solid #666;
            margin-left: 16px;
        }
    }
</style>