Lack of indexed parameters in events

None of the parameters in the events defined in the GovernorBravoDelegateMaia contract are indexed. Consider indexing event parameters to avoid hindering the task of off-chain services searching and filtering for specific events.

Events defined in GovernorBravoInterfaces.sol but emitted in the GovernorBravoDelegateMaia.sol(in Scope).