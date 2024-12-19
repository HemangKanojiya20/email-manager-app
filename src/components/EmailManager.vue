<template>
  <div class="container mx-auto p-6 bg-gray-50 min-h-screen">
    <h1 class="text-3xl font-bold text-gray-700 mb-6">
      Manage Email Recipients
    </h1>
    <div class="flex gap-6">
      <!-- Available Recipients Section -->
      <div class="w-1/2 bg-white shadow-md rounded-lg p-6">
        <h2 class="text-2xl font-semibold text-blue-600 mb-4">
          Available Recipients
        </h2>
        <input
          type="text"
          v-model="search"
          placeholder="Search or add email"
          class="border border-gray-300 w-full p-3 mb-6 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-blue-500"
          @keyup.enter="addNewRecipient"
        />
        <ul class="space-y-4">
          <li
            v-for="(group, domain) in filteredAvailableRecipients"
            :key="domain"
            class="border p-4 rounded-lg hover:shadow-lg transition-shadow duration-200 w-full"
          >
            <div
              class="flex justify-between items-center cursor-pointer"
              @click="toggleExpansion(domain, true)"
            >
              <span class="text-gray-700 font-medium text-lg">
                {{ domain }} ({{ group.length }})
              </span>
              <span class="cursor-pointer text-gray-500 hover:text-blue-600">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="16"
                  height="16"
                  fill="currentColor"
                  class="bi bi-chevron-down"
                  viewBox="0 0 16 16"
                >
                  <path
                    fill-rule="evenodd"
                    d="M4.293 5.293a1 1 0 0 1 1.414 0L8 7.586l2.293-2.293a1 1 0 1 1 1.414 1.414l-3 3a1 1 0 0 1-1.414 0l-3-3a1 1 0 0 1 0-1.414z"
                  />
                </svg>
              </span>
            </div>
            <ul v-if="isAvailableExpanded(domain)" class="pl-6 mt-2 space-y-2">
              <li
                v-for="email in group"
                :key="email"
                class="flex justify-between items-center text-sm"
              >
                <span class="text-gray-600">{{ email }}</span>
                <span
                  class="cursor-pointer text-green-500 hover:text-green-600"
                  @click.stop="toggleSelection(email)"
                >
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="16"
                    height="16"
                    fill="currentColor"
                    class="bi bi-plus-circle"
                    viewBox="0 0 16 16"
                  >
                    <path
                      fill-rule="evenodd"
                      d="M8 0a8 8 0 1 0 0 16A8 8 0 0 0 8 0zM4.5 7a.5.5 0 0 1 .5-.5h2.5V4a.5.5 0 0 1 1 0v2.5h2.5a.5.5 0 0 1 0 1H8v2.5a.5.5 0 0 1-1 0V7H4a.5.5 0 0 1-.5-.5z"
                    />
                  </svg>
                </span>
              </li>
            </ul>
            <div v-if="isAvailableExpanded(domain)" class="mt-2">
              <button
                class="w-full bg-green-500 text-white py-2 rounded-md hover:bg-green-600 transition duration-200"
                @click="addAllToSelected(domain)"
              >
                Add All
              </button>
            </div>
          </li>
        </ul>
      </div>

      <!-- Selected Recipients Section -->
      <div class="w-1/2 bg-white shadow-md rounded-lg p-6">
        <h2 class="text-2xl font-semibold text-green-600 mb-4">
          Selected Recipients
        </h2>
        <ul class="space-y-4">
          <li
            v-for="(group, domain) in groupedSelectedRecipients"
            :key="domain"
            class="border p-4 rounded-lg hover:shadow-lg transition-shadow duration-200 w-full"
          >
            <div
              class="flex justify-between items-center cursor-pointer"
              @click="toggleExpansion(domain, false)"
            >
              <span class="text-gray-700 font-medium text-lg">
                {{ domain }} ({{ group.length }})
              </span>
              <span class="cursor-pointer text-gray-500 hover:text-blue-600">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="16"
                  height="16"
                  fill="currentColor"
                  class="bi bi-chevron-down"
                  viewBox="0 0 16 16"
                >
                  <path
                    fill-rule="evenodd"
                    d="M4.293 5.293a1 1 0 0 1 1.414 0L8 7.586l2.293-2.293a1 1 0 1 1 1.414 1.414l-3 3a1 1 0 0 1-1.414 0l-3-3a1 1 0 0 1 0-1.414z"
                  />
                </svg>
              </span>
            </div>
            <ul v-if="isSelectedExpanded(domain)" class="pl-6 mt-2 space-y-2">
              <li
                v-for="email in group"
                :key="email"
                class="flex justify-between items-center text-sm"
              >
                <span class="text-gray-600">{{ email }}</span>
                <span
                  class="cursor-pointer text-red-500 hover:text-red-600"
                  @click.stop="toggleSelection(email)"
                >
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="16"
                    height="16"
                    fill="currentColor"
                    class="bi bi-dash-circle"
                    viewBox="0 0 16 16"
                  >
                    <path
                      fill-rule="evenodd"
                      d="M8 0a8 8 0 1 0 0 16A8 8 0 0 0 8 0zM5 7a.5.5 0 0 1 .5-.5h5a.5.5 0 0 1 .5.5v1a.5.5 0 0 1-.5.5H5a.5.5 0 0 1-.5-.5V7z"
                    />
                  </svg>
                </span>
              </li>
            </ul>
            <div v-if="isSelectedExpanded(domain)" class="mt-2">
              <button
                class="w-full bg-red-500 text-white py-2 rounded-md hover:bg-red-600 transition duration-200"
                @click="removeAllFromSelected(domain)"
              >
                Remove All
              </button>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import { reactive, computed, ref } from "vue";

export default {
  name: "EmailManager",
  setup() {
    const recipients = reactive([
      { email: "ann@timescale.com", isSelected: false },
      { email: "bob@timescale.com", isSelected: false },
      { email: "brian@qwerty.com", isSelected: false },
      { email: "james@qwerty.com", isSelected: false },
      { email: "jane@awesome.com", isSelected: false },
      { email: "kate@qwerty.com", isSelected: false },
      { email: "mike@hello.com", isSelected: false },
    ]);

    const search = ref("");
    const expandedAvailableDomains = ref([]);
    const expandedSelectedDomains = ref([]);

    const groupByDomain = (list) => {
      return list.reduce((acc, { email }) => {
        const domain = email.split("@")[1];
        acc[domain] = acc[domain] || [];
        acc[domain].push(email);
        return acc;
      }, {});
    };

    const filteredAvailableRecipients = computed(() => {
      const searchTerm = search.value.toLowerCase();
      const filtered = recipients.filter(
        ({ email, isSelected }) =>
          !isSelected &&
          (email.toLowerCase().includes(searchTerm) ||
            email.split("@")[1].toLowerCase().includes(searchTerm))
      );
      return groupByDomain(filtered);
    });

    const groupedSelectedRecipients = computed(() => {
      const selected = recipients.filter(({ isSelected }) => isSelected);
      return groupByDomain(selected);
    });

    const toggleSelection = (email) => {
      const recipient = recipients.find((r) => r.email === email);
      if (recipient) recipient.isSelected = !recipient.isSelected;
    };

    const toggleExpansion = (domain, isAvailable) => {
      if (isAvailable) {
        if (expandedAvailableDomains.value.includes(domain)) {
          expandedAvailableDomains.value =
            expandedAvailableDomains.value.filter((d) => d !== domain);
        } else {
          expandedAvailableDomains.value.push(domain);
        }
      } else {
        if (expandedSelectedDomains.value.includes(domain)) {
          expandedSelectedDomains.value = expandedSelectedDomains.value.filter(
            (d) => d !== domain
          );
        } else {
          expandedSelectedDomains.value.push(domain);
        }
      }
    };

    const isAvailableExpanded = (domain) =>
      expandedAvailableDomains.value.includes(domain);
    const isSelectedExpanded = (domain) =>
      expandedSelectedDomains.value.includes(domain);

    // Add all emails from a domain in available to selected
    const addAllToSelected = (domain) => {
      recipients.forEach((recipient) => {
        if (recipient.email.split("@")[1] === domain && !recipient.isSelected) {
          recipient.isSelected = true;
        }
      });
    };

    // Remove all emails from a domain in selected to available
    const removeAllFromSelected = (domain) => {
      recipients.forEach((recipient) => {
        if (recipient.email.split("@")[1] === domain && recipient.isSelected) {
          recipient.isSelected = false;
        }
      });
    };

    const addNewRecipient = () => {
      const email = search.value.trim();
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      if (emailRegex.test(email)) {
        recipients.push({ email, isSelected: false });
        search.value = "";
      } else {
        alert("Invalid email format");
      }
    };

    return {
      search,
      filteredAvailableRecipients,
      groupedSelectedRecipients,
      toggleSelection,
      toggleExpansion,
      isAvailableExpanded,
      isSelectedExpanded,
      addAllToSelected,
      addNewRecipient,
      removeAllFromSelected,
    };
  },
};
</script>

<style scoped>
.container {
  max-width: 1000px;
  font-family: "Poppins", sans-serif;
}

input {
  font-family: "Poppins", sans-serif;
}

button {
  font-family: "Poppins", sans-serif;
}

ul {
  margin-top: 0;
  padding-left: 0;
}

svg {
  cursor: pointer;
  transition: all 0.2s ease;
}

svg:hover {
  transform: scale(1.2);
}

.text-lg {
  font-size: 1.125rem;
}

.text-sm {
  font-size: 0.875rem;
}
</style>
