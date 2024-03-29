<template>
  <q-page padding>
    <div class="text-h6">
      <q-icon name="business" size="sm" />
      Company Details
    </div>
    <div class="row items-center q-pa-sm">
      <div class="col-3">
        <strong>Name</strong>
      </div>
      <div class="col-9">
        <q-input
          clearable
          dense
          class="q-pb-none"
          :rules="nameRules"
          v-model="state.name"
        />
      </div>
    </div>
    <div class="row items-center q-pa-sm">
      <div class="col-3">
        <strong> Short Name </strong>
      </div>
      <div class="col-9">
        <q-input clearable dense class="q-pb-none" v-model="state.shortName" />
      </div>
    </div>
    <div class="row items-center q-pa-sm">
      <div class="col-3">
        <strong> VAT Number </strong>
      </div>
      <div class="col-9">
        <q-input
          clearable
          dense
          class="q-pb-none"
          :rules="vatRules"
          v-model="state.vat"
        />
      </div>
    </div>
    <div class="row items-center q-pa-sm">
      <div class="col-3">
        <strong> Currency </strong>
      </div>
      <div class="col-9">
        <q-select
          clearable
          class="q-pb-none"
          dense
          :options="currencies"
          v-model="state.currency"
          :rules="currRules"
        />
      </div>
    </div>
    <div class="row items-center q-pa-sm">
      <div class="col-3">
        <strong> Languages </strong>
      </div>
      <div class="col-9">
        <q-select
          clearable
          class="q-pb-none"
          dense
          :options="languages"
          v-model="state.language"
          :rules="langRules"
        />
      </div>
    </div>
    <div class="text-h6 q-mt-xl">
      <q-icon name="import_contacts" size="sm" />
      Addresses
    </div>
    <div class="row q-pa-md items-center">
      <q-card
        v-for="address in state.addresses"
        :key="address.id"
        class="q-pa-sm cursor-pointer"
      >
        <q-card-section>
          <q-icon
            :name="getAddressIcon(address.type)"
            size="md"
            :title="address.type"
          />
          <div class="text-subtitle1 text-no-wrap">{{ address.street }}</div>
          <div class="text-subtitle2">{{ address.zip }}, {{ address.city }}</div>
          <div class="text-overline">{{ address.country }}</div>
        </q-card-section>
      </q-card>
      <q-btn round color="primary" icon="add" size="md" class="addBtn q-ml-lg" />
    </div>
    <div class="text-h6 q-mt-xl">
      <q-icon name="account_balance" size="sm" />
      Bank Accounts
    </div>
    <div class="row q-pa-md q-gutter-sm items-center">
      <q-card
        v-for="bankAcc in state.bankAccounts"
        :key="bankAcc.id"
        class="q-pa-sm"
      >
        <q-card-section>
          <div class="text-subtitle2">{{ bankAcc.bank }}</div>
          <div class="text-overline">{{ bankAcc.label }}</div>
          <div class="text-weight-bold">{{ bankAcc.accountNumber }}</div>
        </q-card-section>
      </q-card>
      <q-btn round color="primary" icon="add" size="md" class="addBtn q-ml-lg" />
    </div>
    <pre class="q-mt-xl">{{ state }}</pre>
  </q-page>
</template>

<script setup>
import { computed, reactive } from "vue";
import { getCompanyDetails } from "src/store/company";
import { getAddressById } from "src/store/address";
import { getBankAccountById } from "src/store/bank";
import { getCurrencies, getCurrencyById } from "src/store/currency";
import { getLanguages, getLangById } from "src/store/language";

const company = computed(() => getCompanyDetails());
const currencies = computed(() => getCurrencies());
const languages = computed(() => getLanguages());

const state = reactive({
  name: company.value.name,
  shortName: company.value.shortName,
  vat: company.value.vatnumber,
  addresses: company.value.addressIds.map((id) => getAddressById(id)),
  bankAccounts: company.value.bankAccountIds.map((id) =>
    getBankAccountById(id)
  ),
  currency: getCurrencyById(company.value.currencyId),
  language: getLangById(company.value.languageId),
});

const nameRules = [(val) => (val && val.length > 0) || "Name can't be empty!"];
const vatRules = [(val) => (val && val.length > 0) || "VAT can't be empty!"];
const currRules = [(val) => !!val || "Please select a Currency!"];
const langRules = [(val) => !!val || "Please select a Language!"];

const getAddressIcon = (type) => {
  console.log("type", type);
  let icon = "home";
  if (type === "invoice") icon = "receipt";
  if (type === "delivery") icon = "local_shipping";
  console.log("icon", icon);
  return icon;
};
</script>

<style lang="scss">
.addBtn {
  width: 40px;
  height: 40px;
}
</style>