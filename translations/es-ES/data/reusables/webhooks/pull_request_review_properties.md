---
ms.openlocfilehash: 6b8d3bb77c6a40a43ab22ffd0e60e61cd049fa61
ms.sourcegitcommit: 47bd0e48c7dba1dde49baff60bc1eddc91ab10c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/05/2022
ms.locfileid: "145069209"
---
Clave | Tipo | Descripción
----|------|------------
`action` | `string` | La acción que se ha realizado. Puede ser una de las siguientes:<ul><li>`submitted`: se ha emitido una solicitud de incorporación de cambios en un estado no pendiente.</li><li>`edited`: se ha editado el cuerpo de una revisión.</li><li>`dismissed`: se ha descartado una revisión.</li></ul>
`pull_request` | `object` | La [solicitud de incorporación de cambios](/rest/reference/pulls) a la que pertenece la revisión.
`review` | `object` | La revisión que se afectó.
`changes[body][from]`|`string` | Versión previa del cuerpo si la acción ha sido `edited`.
