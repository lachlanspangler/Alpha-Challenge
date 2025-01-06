# Liquidations - Tier 3
It’s DeFi Summer, and you run one of the most successful liquidators on Compound v2. On August 20, 2020, you realize that you are losing market share to [this address](https://etherscan.io/tx/0xec4f2ab36afa4fac4ba79b1ca67165c61c62c3bb6a18271c18f42a6bdfdb533d). This is odd because you updated the trading setup after [this proposal](https://compound.finance/governance/proposals/19), and have consistently won almost all liquidations since then.


- a) What’s the edge of this liquidator that allows them to win more liquidations?

The liquidator's edge comes from their ability to act faster and more efficiently than competitors. After the Compound v2 proposal changed the price oracle to use the Open Price Feed, the liquidator likely optimized their bot to better handle this updated system. By quickly reading and acting on updated price data, they could identify liquidation opportunities before others. Additionally, their calldata obfuscation suggests they’re using advanced techniques to execute transactions more efficiently, reducing gas costs and ensuring priority in transaction execution. These optimizations give them an advantage over traditional bots by being faster, cheaper, and more effective in securing liquidation opportunities.

- b) When you figure out the source of the edge, you notice that their calldata is extremely obfuscated. Can you explain on how the calldata works?

The liquidator’s calldata is obfuscated to hide the specific details of how their bot operates and interacts with Compound. This is likely done to prevent competitors from copying their strategy. The calldata might use compressed or hashed formats to reduce gas costs, making it difficult for others to interpret. Essentially, their calldata could encode multiple steps in a single transaction, such as checking price data, submitting a liquidation request, and redistributing funds, all while minimizing execution time. By obfuscating the calldata, they ensure that their methods remain proprietary, giving them a competitive advantage in the liquidation market.

- c) Write code for the bot in Solidity, and provide all necessary information to simulate this liquidation on a mainnet fork.

pragma solidity ^0.8.0;

interface IComptroller {
    function enterMarkets(address[] calldata cTokens) external returns (uint[]);

    function getAccountLiquidity(address account) external view returns (uint, uint, uint);
}

interface ICErc20 {
    function liquidateBorrow(address borrower, uint repayAmount, address cTokenCollateral) external returns (uint);

    function borrowBalanceStored(address account) external view returns (uint);
}

interface IERC20 {
    function approve(address spender, uint amount) external returns (bool);

    function balanceOf(address account) external view returns (uint);
}

contract LiquidationBot {
    IComptroller public comptroller;
    address public owner;

    constructor(address _comptroller) {
        comptroller = IComptroller(_comptroller);
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not authorized");
        _;
    }

    function liquidate(
        address borrower,
        uint repayAmount,
        address cTokenBorrow,
        address cTokenCollateral
    ) external onlyOwner {
        // Approve the cTokenBorrow contract to spend the repay amount
        IERC20 underlying = IERC20(cTokenBorrow);
        underlying.approve(cTokenBorrow, repayAmount);

        // Liquidate the borrow
        ICErc20(cTokenBorrow).liquidateBorrow(borrower, repayAmount, cTokenCollateral);
    }

    function getBorrowBalance(address borrower, address cTokenBorrow) external view returns (uint) {
        return ICErc20(cTokenBorrow).borrowBalanceStored(borrower);
    }

    function getAccountLiquidity(address account) external view returns (uint, uint, uint) {
        return comptroller.getAccountLiquidity(account);
    }
}


Deployment script:
async function main() {
    const [deployer] = await ethers.getSigners();
    console.log("Deploying contracts with the account:", deployer.address);

    const LiquidationBot = await ethers.getContractFactory("LiquidationBot");
    const bot = await LiquidationBot.deploy("0x3d9819210A31b4961b30EF54bE2aeD79B9c9Cd3B"); // Comptroller address

    console.log("Liquidation bot deployed to:", bot.address);
}

main()
    .then(() => process.exit(0))
    .catch((error) => {
        console.error(error);
        process.exit(1);
    });


Liquidations:
async function main() {
    const [deployer] = await ethers.getSigners();
    const LiquidationBot = await ethers.getContractFactory("LiquidationBot");
    const bot = await LiquidationBot.attach("BOT_CONTRACT_ADDRESS");

    const borrower = "BORROWER_ADDRESS";
    const cTokenBorrow = "cToken_BORROW_ADDRESS";
    const cTokenCollateral = "cToken_COLLATERAL_ADDRESS";
    const repayAmount = ethers.utils.parseUnits("100", 18); // Example amount

    console.log("Simulating liquidation...");
    const tx = await bot.liquidate(borrower, repayAmount, cTokenBorrow, cTokenCollateral);
    await tx.wait();

    console.log("Liquidation executed.");
}

main()
    .then(() => process.exit(0))
    .catch((error) => {
        console.error(error);
        process.exit(1);
    });




