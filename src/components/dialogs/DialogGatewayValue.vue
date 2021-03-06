<template>
  <v-dialog v-model="value" max-width="500px" persistent>
    <v-card>
      <v-card-title>
        <span class="headline">{{ title }}</span>
      </v-card-title>

      <v-card-text>
        <v-container grid-list-md>
          <v-form v-model="valid" ref="form" lazy-validation>
            <v-layout wrap>
              <v-flex xs12>
                <v-select
                  v-model="editedValue.device"
                  label="Device"
                  required
                  :rules="[required]"
                  item-text="name"
                  :items="devices"
                ></v-select>
              </v-flex>
              <v-flex xs12>
                <v-select
                  v-model="editedValue.value"
                  label="Value"
                  :hint="editedValue.value ? editedValue.value.help : ''"
                  required
                  return-object
                  :rules="[required]"
                  item-text="label"
                  item-value="value_id"
                  :items="deviceValues"
                >
                 <template slot="selection" slot-scope="data">
                   {{ data.item.label + (data.item.instance > 1 ? " - Instance " +  data.item.instance : "") }}
                 </template>
                  <template slot="item" slot-scope="data">
                    <template>
                      <v-list-tile-content>
                        <v-list-tile-title>{{ data.item.label + (data.item.instance > 1 ? " - Instance " +  data.item.instance : "") }}</v-list-tile-title>
                        <v-list-tile-sub-title style="max-width:500px" class="text-truncate text-no-wrap">{{ data.item.help }}</v-list-tile-sub-title>
                      </v-list-tile-content>
                    </template>
                  </template>
                </v-select>
              </v-flex>
              <v-flex xs12>
                <v-text-field
                  v-model.trim="editedValue.topic"
                  label="Topic"
                  :rules="[requiredTopic]"
                  required
                ></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field
                  v-model="editedValue.postOperation"
                  label="Post operation"
                  hint="Example: '/10' '*100' '+20'"
                  required
                ></v-text-field>
              </v-flex>
              <v-flex xs6>
                <v-switch
                  label="Poll"
                  hint="Enable poll of this value"
                  persistent-hint
                  v-model="editedValue.enablePoll"
                ></v-switch>
              </v-flex>
              <v-flex v-if="editedValue.enablePoll" xs6>
                <v-text-field
                  v-model.number="editedValue.pollIntensity"
                  label="Poll intensity"
                  :rules="[requiredIntensity]"
                  required
                  type="number"
                ></v-text-field>
              </v-flex>

              <v-flex xs6>
                <v-switch
                  label="Verify changes"
                  hint="Verify changes of this value"
                  persistent-hint
                  v-model="editedValue.verifyChanges"
                ></v-switch>
              </v-flex>
            </v-layout>
          </v-form>
        </v-container>
      </v-card-text>

      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat @click="$emit('close')">Cancel</v-btn>
        <v-btn color="blue darken-1" flat @click="$refs.form.validate() && $emit('save')">Save</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
import ValueID from "@/components/ValueId";

export default {
  components: {
    ValueID
  },
  props: {
    value: Boolean,
    gw_type: Number,
    title: String,
    editedValue: Object,
    devices: Array
  },
  watch: {
    value(val) {
      this.$refs.form.resetValidation();
    }
  },
  computed: {
    deviceValues() {
      var device = this.devices.find(d => d.value == this.editedValue.device);
      return device ? device.values : [];
    },
    requiredIntensity() {
      return (
        !this.editedValue.enablePoll ||
        (this.editedValue.enablePoll && this.editedValue.pollIntensity > 0) ||
        "Min value is 1"
      );
    },
    requiredTopic() {
      return this.gw_type != 2 || !!this.editedValue.topic || "Topic required";
    }
  },
  data() {
    return {
      valid: true,
      required: v => !!v || "This field is required"
    };
  }
};
</script>
