# polkadot-staking-utility     
The polkadot-staking-utility script streamlines staking interactions on the Polkadot network, providing a convenient interface for users to manage their staking activities.

Leveraging the Substrate API, this script allows users to check their staking status, nominate validators, and even unbond their DOT tokens. It serves as a practical tool for DOT holders looking to engage in staking without the need for a full-fledged staking application.

from substrateinterface import SubstrateInterface, Keypair

class PolkadotStakingUtility:
    def __init__(self, node_url, keypair):
        self.substrate = SubstrateInterface(url=node_url)
        self.keypair = keypair

    def check_staking_status(self):
        # Check the staking status for the account
        staking_info = self.substrate.query(
            module='Staking',
            storage_function='ErasStakers',
            params=[None, self.keypair.ss58_address, None]
        )
        print(f"Staking Status for Account {self.keypair.ss58_address}:")
        print(f"Total Staked: {staking_info['total']}")
        print(f"Own Stash: {staking_info['own']}")
        print("-" * 30)

    def nominate_validators(self, validators):
        # Nominate a list of validators for staking
        call = self.substrate.compose_call(
            call_module='Staking',
            call_function='nominate',
            call_params={'targets': validators}
        )
        extrinsic = self.substrate.create_signed_extrinsic(call=call, keypair=self.keypair)
        self.substrate.submit_extrinsic(extrinsic, wait_for_inclusion=True)
        print(f"Nomination submitted for validators: {validators}")

    def unbond_tokens(self, amount):
        # Unbond a specified amount of DOT tokens
        call = self.substrate.compose_call(
            call_module='Staking',
            call_function='unbond',
            call_params={'value': amount}
        )
        extrinsic = self.substrate.create_signed_extrinsic(call=call, keypair=self.keypair)
        self.substrate.submit_extrinsic(extrinsic, wait_for_inclusion=True)
        print(f"Unbonding request submitted for {amount} DOT")

# Example Usage
node_url = "wss://rpc.polkadot.io"
alice_keypair = Keypair.create_from_mnemonic("your_alice_mnemonic_here")

staking_utility = PolkadotStakingUtility(node_url, alice_keypair)

# Check staking status
staking_utility.check_staking_status()

# Nominate validators
staking_utility.nominate_validators(['Validator1', 'Validator2'])

# Unbond tokens
staking_utility.unbond_tokens(50)


This script provides a practical utility for Polkadot users interested in managing their staking activities. Users can check their staking status, nominate validators, and unbond DOT tokens, all within a simplified interface. It's a valuable tool for DOT holders engaging in staking on the Polkadot network.
