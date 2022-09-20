<template>
  <q-page class="row">
    <q-form
      @submit.prevent="onSubmit"
      ref="myForm"
    >
      <q-input
        ref="surnameRef"
        filled
        v-model="surname"
        label="Фамилия"
        clearable
        :rules="[
          val => val && val.length > 0 || 'Обязательное поле', 
          val => new RegExp(/^[A-Za-zА-Яа-я]+$/).test(val) || 'Недопустимый формат'
        ]"
      />
      <q-input
        ref="nameRef"
        filled
        v-model="name"
        label="Имя"
        clearable
        :rules="[ 
          val => val && val.length > 0 || 'Обязательное поле',
          val => new RegExp(/^[A-Za-zА-Яа-я]+$/).test(val) || 'Недопустимый формат'
        ]"
      />
      <q-input ref="dateRef" filled v-model="date" mask="date" :rules="['date']" label="Дата рождения" clearable>
        <template v-slot:append>
          <q-icon name="event" class="cursor-pointer">
            <q-popup-proxy cover transition-show="scale" transition-hide="scale">
              <q-date v-model="date">
                <div class="row items-center justify-end">
                  <q-btn v-close-popup label="Close" color="primary" flat />
                </div>
              </q-date>
            </q-popup-proxy>
          </q-icon>
        </template>
      </q-input>
      <q-input
        ref="addressRef"
        filled
        v-model="address"
        label="Домашний адрес"
        clearable
        :rules="[ val => val && val.length > 0 || 'Обязательное поле']"
      />
      <q-btn type="submit">{{ editKey !== null ? 'Редактировать запись' : 'Добавить запись' }} </q-btn>
      <q-btn 
        v-if="editKey !== null"
        @click="revertEdit"
      >
        Отменить редактирование
      </q-btn>
    </q-form>

    <div class="students">
      <p
        class="students--empty" 
        v-if="!students.length"
      >
        Нет записей
      </p>
      <div
        v-for="(student, key) in students"
        :key="key"
        class="students__student"
      >
        <p>Имя: <span>{{ student.name }}</span></p>
        <p>Фамилия: <span>{{ student.surname }}</span></p>
        <p>Дата рождения: <span>{{ student.date }}</span></p>
        <p>Адрес: <span>{{ student.address }}</span></p>

        <div class="students__student--actions">
          <svg
            @click="onEdit(student, key)" 
            xmlns="http://www.w3.org/2000/svg" x="0px" y="0px"
            width="24" height="24"
            viewBox="0 0 24 24"
            style=" fill:#000000;">    
            <path d="M 19.171875 2 C 18.448125 2 17.724375 2.275625 17.171875 2.828125 L 16 4 L 20 8 L 21.171875 6.828125 C 22.275875 5.724125 22.275875 3.933125 21.171875 2.828125 C 20.619375 2.275625 19.895625 2 19.171875 2 z M 14.5 5.5 L 3 17 L 3 21 L 7 21 L 18.5 9.5 L 14.5 5.5 z"></path>
          </svg>
          <svg
            @click="onDelete(key)" 
            xmlns="http://www.w3.org/2000/svg" x="0px" y="0px"
            width="30" height="30"
            viewBox="0 0 30 30"
            style=" fill:#000000;">    <path d="M 14.984375 2.4863281 A 1.0001 1.0001 0 0 0 14 3.5 L 14 4 L 8.5 4 A 1.0001 1.0001 0 0 0 7.4863281 5 L 6 5 A 1.0001 1.0001 0 1 0 6 7 L 24 7 A 1.0001 1.0001 0 1 0 24 5 L 22.513672 5 A 1.0001 1.0001 0 0 0 21.5 4 L 16 4 L 16 3.5 A 1.0001 1.0001 0 0 0 14.984375 2.4863281 z M 6 9 L 7.7929688 24.234375 C 7.9109687 25.241375 8.7633438 26 9.7773438 26 L 20.222656 26 C 21.236656 26 22.088031 25.241375 22.207031 24.234375 L 24 9 L 6 9 z"></path>
          </svg>  
        </div>
      </div>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';

interface IStudent {
  name: string;
  surname: string;
  date: string;
  address: string;
  key: number;
}

const students = ref<Array<IStudent>>([]);
const surname = ref();
const name = ref();
const date = ref();
const address = ref();
const editKey = ref<number | null>(null);
const surnameRef = ref(null);
const nameRef = ref(null);
const dateRef = ref(null);
const addressRef = ref(null);

function onSubmit() {
  if(editKey.value === null) {
    students.value.push({
      name: name.value,
      surname: surname.value,
      date: date.value,
      address: address.value,
      key: students.value.length,
    })
    clearInputs();
    return;
  }

  const student: IStudent | undefined = students.value.find((student) => 
    student.key === editKey.value
  )
  if(student) {
    student.address = address.value;
    student.name = name.value;
    student.date = date.value;
    student.surname = surname.value;
  }
  editKey.value = null;

  clearInputs();
}

function revertEdit() {
  editKey.value = null;
  clearInputs();
}

function clearInputs() {
  address.value = '';
  name.value = '';
  surname.value = '';
  date.value = '';
  dateRef.value.resetValidation();
  surnameRef.value.resetValidation();
  nameRef.value.resetValidation();
  addressRef.value.resetValidation();
}

function onEdit(student: IStudent, key: number) {
  name.value = student.name;
  surname.value = student.surname;
  date.value = student.date;
  address.value = student.address;
  editKey.value = key;
}

function onDelete(key: number) {
  students.value.splice(key, 1);
}
</script>

<style lang="scss" scoped>
.q-page {
  max-width: 700px;
  margin: 0 auto;
  margin-top: 100px;
  display: flex;
  align-items: flex-start;
  justify-content: space-between;

  .q-date {
    margin-bottom: 20px;
  }

  .q-btn {
    width: 100%;
    margin-bottom: 20px;
  }

  .students {
    width: 300px;
    margin-left: 40px;
    border-radius: 8px;
    border: 1px solid #ececec;
    padding: 14px;

    &--empty {
      font-size: 16px;
      font-weight: 500;
      margin-bottom: 0;
      text-align: center;
    }

    &__student {
      padding: 8px 0px;
      border-top: 1px solid #ececec;
      border-bottom: 1px solid #ececec;
      position: relative;

      p {
        font-size: 14px;
        margin-bottom: 8px;

        span {
          font-weight: 700;
        }
      }

      &--actions {
        position: absolute;
        top: 8px;
        right: 4px;
        display: flex;
        flex-direction: column;

        svg {
          cursor: pointer;
          width: 20px;
          height: 20px;
          margin-bottom: 8px;

          &:hover {
            path {
              transition: .3s ease;
              fill: #1976d2;
            }
          }
        }
      }
    }
  }
}
</style>