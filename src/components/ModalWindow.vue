<template>
	<div class="modal" :class="{ modal_opened: isModalOpened }">
		<div class="modal__wrapper">
			<svg
				@click="closeModal"
				class="modal__close-icon"
				width="12"
				height="12"
				viewBox="0 0 12 12"
				fill="none"
				xmlns="http://www.w3.org/2000/svg"
			>
				<path
					d="M12 1.05L10.95 0L6 4.95L1.05 0L0 1.05L4.95 6L0 10.95L1.05 12L6 7.05L10.95 12L12 10.95L7.05 6L12 1.05Z"
					fill="white"
				/>
			</svg>
			<div class="modal__image-wrapper">
				<img
					v-if="props.activeItem?.icon"
					class="modal__image"
					:src="props.activeItem.icon"
					alt="Item icon"
				/>
			</div>
			<div class="modal__info">
				<div class="inventory__skeleton modal__header"></div>
				<ul>
					<li
						class="inventory__skeleton modal__info-item"
						v-for="(info, i) in 5"
						:key="i"
					></li>
				</ul>
			</div>
			<button
				v-if="!isRemoveButtonPressed"
				@click="removeItem"
				class="modal__button modal__button_red"
			>
				Удалить предмет
			</button>
			<div class="modal__accept" v-if="isRemoveButtonPressed">
				<input
					v-model="itemsToRemove"
					class="modal__input"
					placeholder="Введите количество"
					@keypress.enter="acceptRemoveItem"
				/>
				<div class="accept__wrapper">
					<button class="modal__button modal__button_white" @click="closeModal">
						Отмена
					</button>
					<button
						class="modal__button modal__button_red"
						@click="acceptRemoveItem"
					>
						Подтвердить
					</button>
				</div>
			</div>
		</div>
	</div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({ activeItem: Object, isModalOpened: Boolean })
const emits = defineEmits(['closeModal', 'removeItem'])

const isRemoveButtonPressed = ref(false)
const itemsToRemove = ref(null)

const removeItem = () => {
	isRemoveButtonPressed.value = true
}
const acceptRemoveItem = () => {
	isRemoveButtonPressed.value = false
	emits('removeItem', +itemsToRemove.value)
	itemsToRemove.value = null
}
const closeModal = () => {
	isRemoveButtonPressed.value = false
	emits('closeModal')
}
</script>

<style lang="scss" scoped>
.modal {
	width: 250px;
	height: 100%;
	padding: 15px;
	border-left: 1px solid #4d4d4d;
	background: rgba(38, 38, 38, 0.5);
	backdrop-filter: blur(16px);
	position: absolute;
	top: 0;
	right: -100%;
	transition: right 0.2s ease-out;

	&_opened {
		right: 0;
	}

	.modal__close-icon {
		position: absolute;
		top: 14px;
		right: 14px;
		cursor: pointer;
	}

	.modal__image-wrapper {
		width: 130px;
		height: 130px;
		margin: 45px auto 30px;
	}

	.modal__image {
		width: 100%;
		display: block;
	}

	.modal__info {
		padding: 16px 5px 24px;
		margin-bottom: 18px;
		display: flex;
		flex-direction: column;
		align-items: center;
		border-top: 1px solid #4d4d4d;
		border-bottom: 1px solid #4d4d4d;
	}

	.modal__header {
		width: 100%;
		height: 30px;
		margin-bottom: 24px;
		border-radius: 8px;
	}

	.modal__info-item {
		min-width: 190px;
		height: 10px;
		border-radius: 4px;

		&:not(:last-child) {
			margin-bottom: 16px;
		}
	}

	.modal__button {
		padding-top: 11px;
		padding-bottom: 11px;
		border: none;
		border-radius: 8px;
		font-size: 14px;
		color: #fff;
		cursor: pointer;

		&_red {
			width: 100%;
			background: #fa7272;
			transition: all 0.15s ease-out;

			&:hover {
				background-color: #ff8888;
			}
		}

		&_white {
			max-width: 88px;
			width: 100%;
			background: #fff;
			color: #2d2d2d;
			& + .modal__button_red {
				margin-left: 10px;
			}
		}
	}

	.modal__accept {
		padding: 20px;
		position: absolute;
		left: 0;
		bottom: 0;
		background: rgba(38, 38, 38, 0.6);
		border: 1px solid #4d4d4d;
		backdrop-filter: blur(16px);
	}

	.accept__wrapper {
		display: flex;
		align-items: center;
	}

	.modal__input {
		width: 100%;
		padding: 12px;
		margin-bottom: 20px;
		background-color: #262626;
		border: 1px solid #4d4d4d;
		border-radius: 4px;
		color: #fff;

		&::placeholder {
			font-size: 14px;
			font-weight: 500;
			color: #ffffff;
			opacity: 0.4;
		}
	}
}
</style>
