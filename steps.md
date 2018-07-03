steps i'll be following:

- [x] calculate ping and delta between server and client clocks
- [x] advance yourself to server execution
- [x] store inputs
- [x] re-simulate when getting server state
- [x] separate logic loop from render, otherwise logic only gets evaluated every 16ms
- [ ] check whether inputs are arriving at its intended turn
- [no] maybe each input turn has a "sent" property? feels more robust