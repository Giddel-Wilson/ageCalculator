<script lang="ts">
    import { Input } from "$lib/components/ui/input";
    import { Button } from "$lib/components/ui/button";
    import { ArrowDown } from "lucide-svelte";
    import { fade } from "svelte/transition";
  
    let day = "";
    let month = "";
    let year = "";
    let result: { years: number; months: number; days: number } | null = null;
    let errors: Record<string, string> = {};
  
    $: if (day) errors.day = "";
    $: if (month) errors.month = "";
    $: if (year) errors.year = "";
  
    function validateDate() {
      errors = {};
      
      // Required field validation
      if (!day) errors.day = "This field is required";
      if (!month) errors.month = "This field is required";
      if (!year) errors.year = "This field is required";
  
      // Range validation
      const dayNum = parseInt(day);
      const monthNum = parseInt(month);
      const yearNum = parseInt(year);
  
      if (dayNum < 1 || dayNum > 31) errors.day = "Must be a valid day";
      if (monthNum < 1 || monthNum > 12) errors.month = "Must be a valid month";
      if (yearNum > new Date().getFullYear()) errors.year = "Must be in the past";
  
      // Check valid date (e.g., Feb 31st)
      const date = new Date(yearNum, monthNum - 1, dayNum);
      if (date.getDate() !== dayNum) errors.day = "Must be a valid date";
  
      return Object.keys(errors).length === 0;
    }
  
    function calculateAge() {
      if (!validateDate()) return;
  
      const birthDate = new Date(parseInt(year), parseInt(month) - 1, parseInt(day));
      const today = new Date();
  
      let years = today.getFullYear() - birthDate.getFullYear();
      let months = today.getMonth() - birthDate.getMonth();
      let days = today.getDate() - birthDate.getDate();
  
      if (months < 0 || (months === 0 && days < 0)) {
        years--;
        months += 12;
      }
  
      if (days < 0) {
        const lastMonth = new Date(today.getFullYear(), today.getMonth() - 1, birthDate.getDate());
        days = Math.floor((today - lastMonth) / (1000 * 60 * 60 * 24));
        months--;
      }
  
      result = { years, months, days };
    }
  </script>
  
  <div class="min-h-screen bg-gray-100 flex items-center justify-center p-4">
    <div class="bg-white rounded-3xl rounded-br-[100px] p-8 w-full max-w-xl shadow-lg">
      <form 
        on:submit|preventDefault={calculateAge}
        class="flex flex-col gap-8"
      >
        <div class="flex gap-4">
          <div class="flex-1 max-w-[120px]">
            <label 
              for="day" 
              class="text-sm uppercase tracking-widest mb-1 block font-bold {errors.day ? 'text-red-500' : 'text-gray-500'}"
            >
              Day
            </label>
            <Input
              id="day"
              type="number"
              placeholder="DD"
              bind:value={day}
              on:input={() => errors.day = ""}
              class={errors.day ? 'border-red-500' : ''}
            />
            {#if errors.day}
              <span class="text-red-500 text-xs italic mt-1">{errors.day}</span>
            {/if}
          </div>
  
          <div class="flex-1 max-w-[120px]">
            <label 
              for="month"
              class="text-sm uppercase tracking-widest mb-1 block font-bold {errors.month ? 'text-red-500' : 'text-gray-500'}"
            >
              Month
            </label>
            <Input
              id="month"
              type="number"
              placeholder="MM"
              bind:value={month}
              on:input={() => errors.month = ""}
              class={errors.month ? 'border-red-500' : ''}
            />
            {#if errors.month}
              <span class="text-red-500 text-xs italic mt-1">{errors.month}</span>
            {/if}
          </div>
  
          <div class="flex-1 max-w-[120px]">
            <label 
              for="year"
              class="text-sm uppercase tracking-widest mb-1 block font-bold {errors.year ? 'text-red-500' : 'text-gray-500'}"
            >
              Year
            </label>
            <Input
              id="year"
              type="number"
              placeholder="YYYY"
              bind:value={year}
              on:input={() => errors.year = ""}
              class={errors.year ? 'border-red-500' : ''}
            />
            {#if errors.year}
              <span class="text-red-500 text-xs italic mt-1">{errors.year}</span>
            {/if}
          </div>
        </div>
  
        <div class="relative flex items-center mt-2 md:mt-0">
          <div class="flex-1 h-px bg-gray-200"></div>
          <Button
            type="submit"
            class="absolute right-0 rounded-full w-16 h-16 bg-purple-600 hover:bg-black transition-colors duration-200"
          >
            <ArrowDown class="w-8 h-8 text-white" />
          </Button>
        </div>
  
        <div class="text-5xl md:text-6xl font-extrabold italic">
          {#if result}
            <div in:fade>
              <p><span class="text-purple-600">{result.years}</span> years</p>
              <p><span class="text-purple-600">{result.months}</span> months</p>
              <p><span class="text-purple-600">{result.days}</span> days</p>
            </div>
          {:else}
            <div class="text-gray-800 flex flex-col gap-4 md:gap-2">
              <p><span class="text-purple-600">- -</span> years</p>
              <p><span class="text-purple-600">- -</span> months</p>
              <p><span class="text-purple-600">- -</span> days</p>
            </div>
          {/if}
        </div>
      </form>
    </div>
  </div>
