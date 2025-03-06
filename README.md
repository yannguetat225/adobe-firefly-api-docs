# Documentation de l'API Adobe Firefly

Ce dépôt contient des informations sur l'API Adobe Firefly, collectées à partir de diverses sources.

## Liens Officiels

- [Documentation Principale de l'API Firefly](https://developer.adobe.com/firefly-services/docs/firefly-api/)
- [Guides de démarrage](https://developer.adobe.com/firefly-services/docs/guides/get-started/)
- [Guide pour créer votre première application Firefly](https://developer.adobe.com/firefly-services/docs/firefly-api/guides/how-tos/create-your-first-ff-application/)
- [Documentation SDK Firefly](https://developer.adobe.com/firefly-services/docs/guides/sdks/)

## Structure de l'API

Adobe Firefly API est une API REST qui permet d'accéder aux capacités d'intelligence artificielle générative d'Adobe Firefly, telles que la génération d'images à partir de texte, la modification d'images, et plus encore.

L'API semble inclure les fonctionnalités principales suivantes :
- Text-to-Image : Génération d'images à partir de descriptions textuelles
- Glyph-to-Image : Transformation de glyphes (formes vectorielles) en images
- Intégration avec l'API Photoshop : Manipulation d'images et application d'effets
- Intégration avec l'API Lightroom : Édition et amélioration d'images

## Authentification

L'authentification à l'API Adobe Firefly nécessite :
- Un Client ID et un Client Secret, obtenus via Adobe Developer Console
- L'utilisation du protocole OAuth pour obtenir un token d'accès

## Exemples d'utilisation

Basé sur les informations disponibles dans le dépôt [pyfirefly](https://github.com/YodaBotOS/pyfirefly), voici des exemples d'utilisation de l'API :

### Text-to-Image
```python
import asyncio
import aiohttp
import aiofiles

import pyfirefly
from pyfirefly.utils import ImageOptions

token = 'ey...'  # remplacer par votre token bearer

async def create_save_image(a, prompt, img_options, num):
    result = await a.text_to_image(prompt, **img_options)
    async with aiofiles.open(f'{num}.{result.ext}', mode='wb+') as f:
        await f.write(result.image)

async def demo(prompt, num):
    a = await pyfirefly.Firefly(token)
    img = ImageOptions(image_styles = a.image_styles)
    img.add_styles(['Photo', 'Blurry background', 'Origami'])

    tasks = [create_save_image(a, prompt, img.options, i) for i in range(num)]

    await asyncio.gather(*tasks)

asyncio.run(demo('flying cats', 10))
```

### Glyph-to-Image
```python
import asyncio
import aiohttp
import aiofiles

import pyfirefly
from pyfirefly.utils import ImageOptions

token = 'ey...'  # remplacer par votre token bearer

async def create_save_image(a, glyph, img_options, num):
    result = await a.glyph_to_image(glyph, **img_options)
    async with aiofiles.open(f'{num}.{result.ext}', mode='wb+') as f:
        await f.write(result.image)

async def demo(input_fn, num):
    async with aiofiles.open('./glyph.webp', mode='rb') as f:
        glyph = await f.read()

    a = await pyfirefly.Firefly(token)
    img = ImageOptions(text_presets = a.text_presets)
    img.set_text_preset('wires')

    tasks = [create_save_image(a, glyph, img.options, i) for i in range(num)]

    await asyncio.gather(*tasks)

asyncio.run(demo('glyph.webp', 10))
```

## Limitations et Considérations

- L'API Firefly est soumise à des limites de débit qui peuvent varier selon votre plan d'abonnement Adobe
- L'utilisation de l'API nécessite l'acceptation des conditions d'utilisation d'Adobe
- Le contenu généré est soumis aux politiques d'Adobe concernant l'indemnisation IP et la génération responsable d'IA

## Ressources supplémentaires

- [Communauté Adobe Developers](https://experienceleaguecommunities.adobe.com/t5/adobe-developer-questions/ct-p/adobe-developer-questions)
- [GitHub Adobe Docs](https://github.com/AdobeDocs)
- [Bibliothèque Python non-officielle pyfirefly](https://github.com/YodaBotOS/pyfirefly)
