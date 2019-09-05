<template>
  <v-card class="mt-4 elevation-12">
    <v-toolbar dark color="primary">
      <v-toolbar-title>振込が完了しました。</v-toolbar-title>
    </v-toolbar>
    <v-card-text>
      <v-btn nuxt dark color="primary" @click="transfer">
        終了
      </v-btn>
    </v-card-text>
  </v-card>
</template>

<script>
import { mapGetters } from "vuex";

export default {
  middleware: ["hasBank", "hasBranch", "hasAccount"],
  data: () => ({
    valid: true,
  }),
  computed: {
    ...mapGetters("transfer", ["fee", "amount"]),
    ...mapGetters("login", ["account"]),
  },
  methods: {
    transfer() {
      this.transferFrom();
      this.transferTo();
      this.$router.push("/");
    },
    transferFrom() {
      let total = this.account.total;
      total -= this.fee;
      this.$store.dispatch("transfer/payFee", total);
      this.$store.dispatch("transfer/addStatementPayFee", total);
      total -= this.amount;
      this.$store.dispatch("transfer/payTransfer", total);
      this.$store.dispatch("transfer/addStatementPayTransfer", total);
    },
    transferTo() {
      const account = this.$store.getters["transfer/account"];
      let total = account.total;
      total += parseInt(this.amount);
      this.$store.dispatch("transfer/receiveTransfer", total);
      this.$store.dispatch("transfer/addStatementReceiveTransfer", total);
    },
  },
};
</script>
