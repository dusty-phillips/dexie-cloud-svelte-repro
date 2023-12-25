<script lang="ts">
  import Dexie, {type Table} from 'dexie'
  import dexieCloud from "dexie-cloud-addon"


  export class Database extends Dexie {
    friends!: Table<Friend, 'id'>

    constructor() {
      super("Dexie-Svelte-DB", {addons: [dexieCloud]})

      this.version(1).stores({friends: '@id, name, age'})
      
      this.cloud.configure({
        databaseUrl: "https://zge39ibdi.dexie.cloud",
        requireAuth: false
      })
    }
  }

  export const db = new Database()
  const currentUser = db.cloud.currentUser
  console.log($currentUser)
</script>

<h1>Dexie Cloud and Svelte</h1>
<p>
  This illustrates that dexie cloud is not triggering the $currentUser observable
  when the page is first loaded.
</p>
<p>
  When you first visit the page in an authenticated dexie-cloud environment, it will
  render the user as "Unauthorized", which is correct.
</p>
<p>
  When you click the "Log In" button, it will kick off the Dexie cloud authentication flow
  and send an OTP to the user. When the OTP is pasted in, the observable will automatically
  update, which is correct.
</p>
<p>
  However, if you then refresh the page, the name will reflect as "Unauthorized" and never
  update to the user's authenticated name, even though they should still be logged in.
  In fact, if you click the "Log In" button at this time,
  it will change the name to the authorized user WITHOUT triggering the OTP flow. So it seems
  like dexie is not updating the observable on page load to reflect the user is logged in.
</p>
<p>
  Current User: {$currentUser.name}
</p>

<button on:click={() => db.cloud.login()}>Log In</button>
<button on:click={() => db.cloud.logout()}>Log Out</button>
