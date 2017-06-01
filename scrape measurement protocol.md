Parameter Documentation can be easly scraped off of the [Measurement protocol documentation](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters)

Run this JS in the Dev Console and Chrome will copy the JS of Parameters to your clipboard.

the code is extremly ugly und should propably be refined

```javascript
copy((()=>{
	let docuDomNodes = document.querySelector('div[itemprop="articleBody"]').querySelectorAll('h2,h3,div.ind'),
		nodeIndex = 0,
		results = [],
		param = {},
		category = '';

	while(node = docuDomNodes[nodeIndex++]){ //next will be undefined once itterated
		switch(node.tagName){
			case 'H2': category = node.innerText; break;
			case 'H3':
				param.documentationAnchor = node.id;
				param.name = node.innerText;
				param.category = category;
				break;
			case 'DIV':
				param.required = !node.children[0].innerText.match(/Optional/);
				param.description = node.children[1].innerText;

				let td = node.querySelectorAll('tr:nth-child(2) > td');
				param.keyRegex = '^' + td[0].innerText.replace(/<\w+>/g,'(\\d+)') + '$';
				if(keyMatches = td[0].innerText.match(/<\w+>/g,'(\\d+)')){
					param.keyRegexMatches = keyMatches.map((m)=>m.slice(1,-1));
				}
				param.valueType = td[1].innerText;
				param.defaultValue = td[2].innerText;
				param.maxLength = td[3].innerText;
				param.supportedHitTypes = td[4].innerText;

				param.exampleValue = node.querySelector('div > code:nth-child(1)').innerText;
				param.exampleUsage = node.querySelector('div > code:nth-child(3)').innerText;
				results.push(param);
				param = {};
		}
	}

	return results;
})())

```
