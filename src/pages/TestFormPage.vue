<template>
  <q-page padding>
    <!-- content -->
    <div class="q-pa-md">
      <div class="row justify-around">
        <div class="q-gutter-md col-12 col-md-8">
          <q-form
            @submit="onChangeSettingsSubmit"
            @reset="onReset"
          >
            <div class="row justify-around">
              <div class="col q-gutter-md" style="max-width: 300px">
                <q-input
                  filled
                  type="string"
                  name="testName"
                  v-model="testName"
                  label="Test_name"
                  lazy-rules
                  :rules="[ val => val && val.length > 0 || 'Please type something']"
                />
                <q-input
                  filled
                  type="string"
                  name="testSurname"
                  v-model="testSurname"
                  label="Test_surname"
                  lazy-rules
                  :rules="[ val => val && val.length > 0 || 'Please type something']"
                />
                <q-input
                  filled
                  type="string"
                  name="testPatrname"
                  v-model="testPatrname"
                  label="Test_patrname"
                  lazy-rules
                  :rules="[ val => val && val.length > 2 || 'Please type something']"
                />
              </div>
              <div class="col q-gutter-md" style="max-width: 300px">
                <q-input
                  filled
                  type="email"
                  name="email"
                  v-model="email"
                  label="Test_email"
                  lazy-rules
                  :rules="[ val => val && val.length > 0 || 'Please type something', (val) => validateEmail(val) || 'Must be a valid email.']"
                />

                <q-input
                  filled
                  type="tel"
                  name="tel"
                  v-model="tel"
                  label="Test_tel"
                  lazy-rules
                  :rules="[ val => val && val.length > 0 || 'Please type something']"
                />

                <!--          <q-input-->
                <!--            filled-->
                <!--            type="number"-->
                <!--            v-model="age"-->
                <!--            label="Your age *"-->
                <!--            lazy-rules-->
                <!--            :rules="[-->
                <!--          val => val !== null && val !== '' || 'Please type your age',-->
                <!--          val => val > 0 && val < 100 || 'Please type a real age'-->
                <!--        ]"-->
                <!--          />-->

                <!--          <q-toggle v-model="accept" label="I accept the license and terms"/>-->

                <div>
                  <q-btn label="Сохранить изменения" type="submit" color="primary" class="q-mt-md"/>
                  <q-btn label="Отменить" type="reset" color="deep-orange" class="q-mt-md"/>
                </div>
              </div>
            </div>
          </q-form>
        </div>
        <div class="q-gutter-md col-12 col-md-4" style="max-width: 300px">
          <q-form
            @submit="onPassSubmit"
          >
            <q-input
              filled
              type="password"
              label="Введите старый пароль"
              lazy-rules
              name="oldPass"
              v-model="oldPass"
              :rules="[ val => val && val.length > 0 || 'Please type something']"
            />
            <q-input
              filled
              type="password"
              label="Введите новый пароль"
              name="newPass"
              v-model="newPass"
              lazy-rules
              :rules="[val => val && val.length > 0 || 'Please type something' ]"
            />
            <div>
              <q-btn label="Подтвердить" type="submit" color="primary"/>
            </div>
          </q-form>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script lang="ts">
import {defineComponent, ref} from 'vue'
import {Notify, useQuasar} from 'quasar'

export default defineComponent({
  plugins: {Notify},
  name: 'TestFormPage',
  setup() {
    const $q = useQuasar()
    const testName = ref(null);
    const email = ref(null);
    const testSurname = ref(null);
    const testPatrname = ref(null);
    const tel = ref(null);


    return {
      // for change settings
      testName,
      testSurname,
      testPatrname,
      email,
      tel,
      validateEmail(email: string): boolean {
        return /[a-z0-9]+@[a-z]+\.[a-z]{2,3}/.test(email);
      },
      async onChangeSettingsSubmit(evt: any) {

        const formData = new FormData(evt.target)
        const res = await fetch('/change-pass', {
          method: 'Post',
          body: formData
        })
        if (res.ok) {
          $q.notify({
            message: 'Настройки изменены',
            color: 'green'
          })
        } else {
          $q.notify({
            message: `Ошибка ${res.statusText}`,
            color: 'red'
          })
        }
      },
      onReset() {
        testName.value = null;
        email.value = null;
        testSurname.value = null;
        testPatrname.value = null;
        tel.value = null;
        console.log('reset all values in field')
      },


      // for pass
      oldPass: ref(''),
      newPass: ref(''),
      async onPassSubmit(evt: any) {
        const formData = new FormData(evt.target)

        // Пока не делал. Возможно надо что-то предварительно сделать с данными?
        const data = []
        for (const [oldPass, newPass] of formData.entries()) {
          data.push({
            oldPass,
            newPass
          })
        }
        // отправить данные - data
        // fetch или axios
        const res = await fetch('/change-pass', {
          method: 'Post',
          body: formData
        })
        if (res.ok) {
          $q.notify({
            message: 'Пароль изменен',
            color: 'green'
          })
        } else {
          $q.notify({
            message: `Ошибка ${res.statusText}`,
            color: 'red'
          })
        }
      }
    }
  }
});

// Недоделал для валидации пароля
// Валидация на кол-во символов > 12
// Одна заглавная буква, одна цифра и один символ
//
// import {defineComponent} from 'vue'
// import {reactive} from 'vue';
//
// // START для формы изменения пароля
// // стейт для пароля
// type PasswordFormState = {
//   oldPass: string;
//   newPass: string;
// };
//
// type PasswordValidator = {
//   length: boolean;
//   capital: boolean;
//   number: boolean;
//   symbol: boolean;
// };
//
// const changePasswordFormState = reactive(<PasswordFormState>{
//   oldPass: '',
//   newPass: '',
// });
//
// const validPassword = reactive(<PasswordValidator>{
//   length: false,
//   capital: false,
//   number: false,
//   symbol: false,
// });
//
//
// function validatePassword(password: string): boolean {
//   // Test length
//   validPassword.length = changePasswordFormState.newPass.length >= 12;
//   // Test capital
//   validPassword.capital = /^(?=.*[A-Z]).*$/.test(password);
//   // Test number
//   validPassword.number = /^(?=.*[0-9]).*$/.test(password);
//   // Test symbol
//   validPassword.symbol = /^(?=.*[!@#$%^&*()\-_+=]).*$/.test(password);
//   return (
//     validPassword.length &&
//     validPassword.capital &&
//     validPassword.number &&
//     validPassword.symbol
//   );
// }
//
// function submitChangePasswordForm(): void {
//   console.log('changePasswordFormState', changePasswordFormState);
// }
//
// // END для формы изменения пароля
//
//
// // START для формы изменения данных пользователя
// function validateEmail(email: string): boolean {
//   return /[a-z0-9]+@[a-z]+\.[a-z]{2,3}/.test(email);
// }
//
// // END конец для формы изменения данных пользователя
//
// export default defineComponent({
//   name: 'TestFormPage'
// })
</script>
