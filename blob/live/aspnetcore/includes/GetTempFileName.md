<span data-ttu-id="7c01c-101">**Предупреждение**: В следующем коде используется `GetTempFileName`, какие вызывает `IOException` Если превышает 65 535 файлы создаются без удаления предыдущих временные файлы.</span><span class="sxs-lookup"><span data-stu-id="7c01c-101">**Warning**: The following code uses `GetTempFileName`, which throws an `IOException` if more than 65535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="7c01c-102">Настоящее приложение следует удалить временные файлы или использовать `GetTempPath` и `GetRandomFileName` для создания имен временных файлов.</span><span class="sxs-lookup"><span data-stu-id="7c01c-102">A real app should either delete temporary files or use `GetTempPath` and `GetRandomFileName` to create temporary file names.</span></span> <span data-ttu-id="7c01c-103">Ограничение 65535 файлов: на каждом сервере, так что другое приложение на сервере можно использовать все файлы до 65535.</span><span class="sxs-lookup"><span data-stu-id="7c01c-103">The 65535 files limit is per server, so another app on the server can use up all 65535 files.</span></span> 